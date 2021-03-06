---
title: 依 CloudSimple 的 Azure VMware 解決方案-防火牆資料表
description: 深入瞭解 CloudSimple 私用雲端防火牆資料表和防火牆規則。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877721"
---
# <a name="firewall-tables-overview"></a>防火牆資料表總覽

防火牆資料表會列出規則, 以篩選進出私人雲端資源的網路流量。 您可以將防火牆資料表套用至 VLAN/子網。 這些規則會控制來源網路或 IP 位址與目的地網路或 IP 位址之間的網路流量。

## <a name="firewall-rules"></a>防火牆規則

下表描述防火牆規則中的參數。

| 屬性 | 詳細資料 |
| ---------| --------|
| **名稱** | 唯一識別防火牆規則及其用途的名稱。 |
| **優先順序** | 介於100和4096之間的數位, 100 為最高優先順序。 規則會依照優先順序進行處理。 當流量遇到規則相符的情況時, 規則處理就會停止。 因此, 優先順序較低的規則不會處理具有較高優先順序之規則的相同屬性。  請小心避免規則衝突。 |
| **狀態追蹤** | 追蹤可以是無狀態 (私用雲端、網際網路或 VPN) 或具狀態 (公用 IP)。  |
| **通訊協定** | 選項包括 [Any]、[TCP] 或 [UDP]。 如果您需要 ICMP, 請使用任何。 |
| **Direction** | 規則是否套用至輸入或輸出流量。 |
| **動作** | [允許] 或 [拒絕] 規則中定義的流量類型。 |
| **Source** | IP 位址、無類別網域間路由 (CIDR) 區塊 (例如 10.0.0.0/24) 或任何。  指定範圍、服務標籤或應用程式安全性群組, 可讓您建立較少的安全性規則。 |
| **來源埠** | 從中產生網路流量的埠。  您可以指定個別埠或埠範圍, 例如443或8000-8080。 指定範圍可讓您建立較少的安全性規則。 |
| **位置** | IP 位址、無類別網域間路由 (CIDR) 區塊 (例如 10.0.0.0/24) 或任何。  指定範圍、服務標籤或應用程式安全性群組, 可讓您建立較少的安全性規則。  |
| **目的地埠** | 網路流量流向的埠。  您可以指定個別埠或埠範圍, 例如443或8000-8080。 指定範圍可讓您建立較少的安全性規則。|

### <a name="stateless"></a>無狀態

無狀態規則只會查看個別封包, 並根據規則進行篩選。  
相反方向的流量可能需要額外的規則。  針對下列各點之間的流量使用無狀態規則:

* 私人雲端的子網
* 內部部署子網和私人雲端子網
* 來自私人雲端的網際網路流量

### <a name="stateful"></a>狀態

 可設定狀態的規則會感知通過它的連接。 系統會為現有連線建立流程記錄。 允許或拒絕通訊都會以此流程記錄的連線狀態為依據。  針對 [公用 IP 位址] 使用此規則類型來篩選來自網際網路的流量。

### <a name="default-rules"></a>預設規則

系統會在每個防火牆資料表上建立下列預設規則。

|Priority|Name|狀態追蹤|Direction|流量類型|Protocol|Source|來源連接埠|目的地|目的地通訊埠|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|狀態|傳出|公用 IP 或網際網路流量|全部|Any|Any|Any|Any|允許|
|65001|deny-all-from-internet|狀態|傳入|公用 IP 或網際網路流量|全部|Any|Any|Any|Any|拒絕|
|65002|allow-all-to-intranet|無狀態|傳出|私人雲端內部或 VPN 流量|全部|Any|Any|Any|Any|允許|
|65003|allow-all-from-intranet|無狀態|傳入|私人雲端內部或 VPN 流量|全部|Any|Any|Any|Any|允許|

## <a name="next-steps"></a>後續步驟

* [設定防火牆資料表和規則](firewall.md)
