---
title: 建立 a.Net Core 應用程式並將其發佈至遠端 Linux 叢集
description: 從 Visual Studio 建立併發布以遠端 Linux 叢集為目標的 .Net Core 應用程式
author: peterpogorski
ms.topic: troubleshooting
ms.date: 5/20/2019
ms.author: pepogors
ms.openlocfilehash: c30eedb6782e4172d677f16e27441f28c78cdd89
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614344"
---
# <a name="use-visual-studio-to-create-and-publish-net-core-applications-targeting-a-remote-linux-service-fabric-cluster"></a>使用 Visual Studio 來建立和發佈以遠端 Linux Service Fabric 叢集為目標 .Net Core 應用程式
透過 Visual Studio 工具，您可以開發和發佈以 Linux Service Fabric 叢集為目標的 Service Fabric .Net Core 應用程式。 SDK 版本必須是3.4 或更新版本，才能部署目標為 Linux Service Fabric 叢集的 .Net Core 應用程式，Visual Studio。

> [!Note]
> Visual Studio 不支援對以 Linux 為目標的 Service Fabric 應用程式進行偵錯工具。
>

## <a name="create-a-service-fabric-application-targeting-net-core"></a>建立以 .Net Core 為目標的 Service Fabric 應用程式
1. 以**系統管理員**身分啟動 Visual Studio。
2. 建立具有檔案 **> 新 > 專案**的專案。
3. 在 [**新增專案**] 對話方塊中，選擇 [**雲端 > Service Fabric 應用程式**]。
![建立-應用程式]
4. 為應用程式命名，然後按一下 **[確定]** 。
5. 在 [**新增 Service Fabric 服務**] 頁面上，選取您想要在 [ **.Net Core] 區段**底下建立的服務類型。
![建立-服務]

## <a name="deploy-to-a-remote-linux-cluster"></a>部署到遠端 Linux 叢集
1. 在 [方案瀏覽器] 中，以滑鼠右鍵按一下應用程式，然後選取 [**組建**]。
![組建-應用程式]
2. 一旦應用程式的組建進程完成後，以滑鼠右鍵按一下該服務，然後選取 [編輯 **.csproj**檔案]。
![編輯-.csproj]
3. 如果服務是動作**專案類型，請**將 UpdateServiceFabricManifestEnabled 屬性從 True 編輯為**False** 。 如果您的應用程式沒有動作專案服務，請跳至步驟4。
```xml
    <UpdateServiceFabricManifestEnabled>False</UpdateServiceFabricManifestEnabled>
```
> [!Note]
> 將 UpdateServiceFabricManifestEnabled 設定為 false，將會在組建期間停用 ServiceManifest 的更新。 任何變更（例如 [新增]、[移除] 或 [重新命名]）都不會反映在 ServiceManifest 中。 如果進行任何變更，您必須手動更新 ServiceManifest，或暫時將 UpdateServiceFabricManifestEnabled 設定為 true，並建立將更新 ServiceManifest 的服務，然後將它還原回 false。
>

4. 將 RuntimeIndetifier 從 win7-x64 更新為服務專案中的目標平臺。
```xml
    <RuntimeIdentifier>ubuntu.16.04-x64</RuntimeIdentifier>
```
5. 在 ServiceManifest 中，更新 entrypoint 程式以移除 .exe。 
```xml
    <EntryPoint> 
    <ExeHost> 
        <Program>Actor1</Program> 
    </ExeHost> 
    </EntryPoint>
```
6. 在方案總管中，以滑鼠右鍵按一下應用程式，然後選取 [**發佈**]。 [發佈] 對話方塊隨即出現。
7. 在 [連線**端點**] 中，選取您想要設為目標之遠端 Service Fabric Linux 叢集的端點。
![發佈-應用程式]

<!--Image references-->
[建立-應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-application-remote-linux.png
[建立-服務]:./media/service-fabric-how-to-vs-remote-linux-cluster/create-service-remote-linux.png
[組建-應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/build-application-remote-linux.png
[編輯-.csproj]:./media/service-fabric-how-to-vs-remote-linux-cluster/edit-csproj-remote-linux.png
[發行-應用程式]:./media/service-fabric-how-to-vs-remote-linux-cluster/publish-remote-linux.png

## <a name="next-steps"></a>後續步驟
* 瞭解如何[使用 .Net Core 開始使用 Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-dotnet-core-getting-started/)
