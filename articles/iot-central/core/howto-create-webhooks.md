---
title: 在 Azure IoT Central 中建立規則的 Webhook | Microsoft Docs
description: 在 Azure IoT Central 中建立 Webhook，以在規則引發時自動通知其他應用程式。
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953664"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>在 Azure IoT Central 中建立規則的 Webhook 動作

本主題適用於建置人員和系統管理員。

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Webhook 可讓您將 IoT Central 應用程式連線至其他應用程式和服務，以進行遠端監視和通知。 Webhook 會在 IoT Central 應用程式中觸發規則時，自動通知您所連線的其他應用程式和服務。 每當觸發規則時，您的 IoT Central 應用程式就會將 POST 要求傳送至其他應用程式的 HTTP 端點。 承載包含裝置詳細資料和規則引發程式詳細資料。

## <a name="set-up-the-webhook"></a>設定 webhook

在此範例中，您會連接到 RequestBin，以在使用 webhook 引發規則時收到通知。

1. 開啟 [RequestBin](https://requestbin.net/)。

1. 建立新的 RequestBin，並複製 **Bin URL**。

1. 建立[遙測規則](howto-create-telemetry-rules.md)該[事件規則](howto-create-event-rules.md)。 儲存規則，然後新增動作。

    ![Webhook 建立畫面](media/howto-create-webhooks/webhookcreate.png)

1. 選擇 Webhook 動作並提供顯示名稱，然後貼上 Bin URL 作為回呼 URL。

1. 儲存規則。

現在當觸發規則時，您會在 RequestBin 中看到新的要求。

## <a name="payload"></a>Payload

觸發規則時，將會對回呼 URL 提出 HTTP POST 要求，其中包含具有測量、裝置、規則和應用程式詳細資料的 JSON 承載。 對於遙測規則，承載看起來如下所示：

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>已知限制

目前並無法以程式設計方式透過 API 來訂閱/取消訂閱這些 Webhook。

如果您有任何改進這項功能的構想，請將您的建議張貼到我們的 [Uservoice 論壇](https://feedback.azure.com/forums/911455-azure-iot-central)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何設定和使用 webhook，建議的下一個步驟是探索[Microsoft Flow 中的建立工作流程](howto-add-microsoft-flow.md)。
