---
title: 公開 WebSocket 伺服器以應用程式閘道
description: 本文提供有關如何使用 AKS 叢集的輸入控制器，向應用程式閘道公開 WebSocket 伺服器的資訊。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 01fde82e69917f59f6519524c4c8828feb84a4f9
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795973"
---
# <a name="expose-a-websocket-server-to-application-gateway"></a>公開 WebSocket 伺服器以應用程式閘道

如應用程式閘道 v2 檔中所述，它會[提供 WebSocket 和 HTTP/2 通訊協定的原生支援](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic)。 請注意，這兩個應用程式閘道和 Kubernetes 輸入都沒有使用者可設定的設定，可選擇性地啟用或停用 WebSocket 支援。

以下的 Kubernetes 部署 YAML 顯示用來部署 WebSocket 伺服器的最低設定，這與部署一般 web 伺服器相同：
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: websocket-server
spec:
  selector:
    matchLabels:
      app: ws-app
  replicas: 2
  template:
    metadata:
      labels:
        app: ws-app
    spec:
      containers:
        - name: websocket-app
          imagePullPolicy: Always
          image: your-container-repo.azurecr.io/websockets-app
          ports:
            - containerPort: 8888
      imagePullSecrets:
        - name: azure-container-registry-credentials

---

apiVersion: v1
kind: Service
metadata:
  name: websocket-app-service
spec:
  selector:
    app: ws-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8888

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-repeater
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: ws.contoso.com
      http:
        paths:
          - backend:
              serviceName: websocket-app-service
              servicePort: 80
```

假設所有必要條件皆已完成，且您的 AKS 中有 Kubernetes 輸入所控制的應用程式閘道，上述部署會導致 Websocket 伺服器公開于應用程式閘道公用 IP 的埠80和 `ws.contoso.com`domain.

下列捲曲的命令會測試 WebSocket 伺服器部署：
```sh
curl -i -N -H "Connection: Upgrade" \
        -H "Upgrade: websocket" \
        -H "Origin: http://localhost" \
        -H "Host: ws.contoso.com" \
        -H "Sec-Websocket-Version: 13" \
        -H "Sec-WebSocket-Key: 123" \
        http://1.2.3.4:80/ws
```

## <a name="websocket-health-probes"></a>WebSocket 健全狀況探查

如果您的部署並未明確定義健康情況探查，應用程式閘道會嘗試在您的 WebSocket 伺服器端點上進行 HTTP GET。
視伺服器的執行而定（[這裡是一個我們愛](https://github.com/gorilla/websocket/blob/master/examples/chat/main.go)用的），可能需要 WebSocket 特定標頭（例如`Sec-Websocket-Version`）。
因為應用程式閘道不會新增 WebSocket 標頭，所以應用程式閘道的 WebSocket 伺服器健全狀況探查回應很可能會 `400 Bad Request`。
因此應用程式閘道會將您的 pod 標示為狀況不良，最後會導致 WebSocket 伺服器取用者的 `502 Bad Gateway`。
若要避免這種情況，您可能需要將健康狀態檢查的 HTTP GET 處理常式新增至您的伺服器（`/health` 實例，這會傳回 `200 OK`）。
