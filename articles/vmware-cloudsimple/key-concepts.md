---
title: 透過 CloudSimple 管理 VMware 解決方案的重要概念
titleSuffix: Azure VMware Solution by CloudSimple
description: 說明透過 CloudSimple 管理 Azure VMware 解決方案的重要概念
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d4de25f281f9164d5cfe3de64d2ca644593a8dd3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450789"
---
# <a name="key-concepts-for-administration-of-azure-vmware-solution-by-cloudsimple"></a>透過 CloudSimple 管理 Azure VMware 解決方案的重要概念

若要管理 Azure VMware Solution by CloudSimple，必須先瞭解下列概念：

* CloudSimple 服務，會顯示為 Azure VMware Solution by CloudSimple-服務
* CloudSimple 節點，會顯示為 Azure VMware Solution by CloudSimple-Node
* CloudSimple 私用雲端
* 服務網路
* CloudSimple 虛擬機器，其會顯示為 Azure VMware Solution by CloudSimple-虛擬機器

## <a name="cloudsimple-service"></a>CloudSimple 服務

透過 CloudSimple 服務，您可以從 Azure 入口網站 CloudSimple，以建立及管理與 VMware 解決方案相關聯的所有資源。 在您想要使用服務的每個區域中建立服務資源。

深入瞭解[CloudSimple 服務](cloudsimple-service.md)。

## <a name="cloudsimple-node"></a>CloudSimple 節點

CloudSimple 節點是在其中部署 VMware ESXi 程式管理元件的專用、裸機、超大範圍計算和儲存體主機。 此節點接著會併入 VMware vSphere、vCenter、vSAN 和 NSX 平臺中。 CloudSimple 網路服務和 edge 網路服務也會一併啟用。 每個節點都可做為計算和儲存體容量的單位，以供您布建以建立[CloudSimple 私人](cloudsimple-private-cloud.md)雲端。 您在可用 CloudSimple 服務的區域中布建或保留節點。

深入瞭解[CloudSimple 節點](cloudsimple-node.md)。

## <a name="cloudsimple-private-cloud"></a>CloudSimple 私用雲端

CloudSimple 私用雲端是一種隔離的 VMware 堆疊環境，受其本身管理網域中的 vCenter server 管理。 VMware 堆疊包含 ESXi 主機、vSphere、vCenter、vSAN 和 NSX。 堆疊會在專用節點（專用和隔離的裸機硬體）上執行，並由使用者透過包含 vCenter 和 NSX Manager 的原生 VMware 工具取用。 專用節點會部署在 Azure 位置，並由 Azure 管理。 您可以使用網路服務（例如 Vlan 和子網和防火牆表格）來分割和保護每個私人雲端。 您的內部部署環境與 Azure 網路的連線是使用安全、私人 VPN 和 Azure ExpressRoute 連線所建立。

深入瞭解[CloudSimple 私用雲端](cloudsimple-private-cloud.md)。

## <a name="service-networking"></a>服務網路

CloudSimple 服務會為您的 CloudSimple 服務部署所在的每個區域提供一個網路。 網路是單一的 TCP 第3層位址空間，預設會啟用路由。 在此區域中建立的所有私人雲端和子網不需要進行任何額外設定即可彼此通訊。 您可以使用 Vlan 在 vCenter 上建立分散式通訊埠群組。 您可以使用下列網路功能來設定和保護私人雲端中的工作負載資源：

* [Vlan 和子網](cloudsimple-vlans-subnets.md)
* [防火牆資料表](cloudsimple-firewall-tables.md)
* [VPN 閘道](cloudsimple-vpn-gateways.md)
* [公用 IP](cloudsimple-public-ip-address.md)
* [Azure 網路連線](cloudsimple-azure-network-connection.md)

## <a name="cloudsimple-virtual-machine"></a>CloudSimple 虛擬機器

您可以使用 CloudSimple 服務，從 Azure 入口網站管理 VMware 虛擬機器。 您 vSphere 環境中的一或多個叢集或資源集區，可以對應到建立服務所在的訂用帳戶。

深入了解：

* [CloudSimple 虛擬機器](cloudsimple-virtual-machines.md)
* [Azure 訂用帳戶對應](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)
