---
title: 使用 Azure Migrate 來評估 Hyper-V VM 是否可移轉至 Azure | Microsoft Docs
description: 說明如何使用 Azure Migrate 來評估內部部署 Hyper-V VM 是否可移轉至 Azure。
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: f36b0fbae01b25e604222c76d41ac21c0a7ae5a9
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029020"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>使用 Azure Migrate 伺服器評估來評估 Hyper-V VM

本文說明如何使用 Azure Migrate 來評估內部部署 Hyper-V VM：伺服器評量工具。

[Azure Migrate](migrate-services-overview.md) 會提供工具中樞，協助您探索和評估應用程式、基礎結構和工作負載，並且將這些項目遷移至 Microsoft Azure。 此中樞包含 Azure Migrate 工具和第三方獨立軟體廠商 (ISV) 供應項目。



本教學課程是一個系列中的第二篇，會示範如何評估 Hyper-V VM 並將其遷移至 Azure。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 Azure Migrate 專案。
> * 設定和註冊 Azure Migrate 設備。
> * 開始連續探索內部部署 VM。
> * 將探索到的 VM 群組在一起，然後評估該群組。
> * 檢閱評估結果。

> [!NOTE]
> 教學課程將會針對案例示範最簡單的部署路徑，讓您可以快速設定概念證明。 教學課程在情況允許時都會使用預設選項，且不會顯示所有可能的設定與路徑。 如需詳細指示，請檢閱操作說明文章。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prerequisites"></a>Prerequisites

- [完成](tutorial-prepare-hyper-v.md)本系列的第一個教學課程。 如果未完成，本教學課程中的指示便沒有作用。
- 您在第一個教學課程中應該已完成下列作業：
    - 針對 Azure Migrate [設定 Azure 權限](tutorial-prepare-hyper-v.md#prepare-azure)。
    - [準備 Hyper-V](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) 叢集、主機和 VM 以便進行評估。
    - [準備 Azure Migrate 設備的部署](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment)，以用於 Hyper-V VM 的探索和評量。

## <a name="set-up-an-azure-migrate-project"></a>設定 Azure Migrate 專案

1. 在 Azure 入口網站 > [所有服務]  中，搜尋 **Azure Migrate**。
2. 在搜尋結果中，選取 [Azure Migrate]  。
3. 在 [概觀]  的 [探索、評估和遷移伺服器]  底下，按一下 [評估和遷移伺服器]  。

    ![探索和評估伺服器](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. 在 [開始使用]  中，按一下 [新增工具]  。
5. 在 [遷移專案]  索引標籤中，選取您的 Azure 訂用帳戶，並建立資源群組 (如果您還沒有資源群組的話)。
6. 在 [專案詳細資料]  中，指定專案名稱以及要在其中建立專案的區域。


    ![建立 Azure Migrate 專案](./media/tutorial-assess-hyper-v/migrate-project.png)

    您可以在這些區域建立 Azure Migrate 專案。

    **地理位置** | **區域**
    --- | ---
    Asia  | 東南亞
    歐洲 | 北歐或西歐
    United Kingdom |  英國南部或英國西部
    美國 | 美國東部、美國西部 2 或美國中西部

    - 專案區域只會用來儲存從內部部署 VM 收集到的中繼資料。
    - 遷移 VM 時可選取不同的 Azure 目標區域。 所有 Azure 區域都支援作為移轉目標。

7. 按 [下一步]  。
8. 在 [選取評量工具]  中，選取 **[Azure Migrate：伺服器評量]**  > [下一步]  。

    ![建立 Azure Migrate 專案](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. 在 [選取移轉工具]  中，選取 [暫時跳過新增移轉工具]   > [下一步]  。
10. 在 [檢閱 + 新增工具]  中檢閱設定，然後按一下 [新增工具]  。
11. 等候幾分鐘讓 Azure Migrate 專案完成部署。 您會進入專案頁面。 如果您沒有看到專案，則可以從 Azure Migrate 儀表板中的 [伺服器]  來存取。




## <a name="set-up-the-appliance-vm"></a>設定設備 VM

Azure Migrate 伺服器評估會執行輕量型 Hyper-V VM 設備。

- 此設備會執行 VM 探索，並將 VM 的中繼資料和效能資料傳送至 Azure Migrate：伺服器評量。
- 若要設定設備，請：
    - 從 Azure 入口網站下載壓縮的 Hyper-V VHD。
    - 建立設備，並確認其可以連線至 Azure Migrate 伺服器評估。
    - 第一次設定設備，並向 Azure Migrate 專案進行註冊。

### <a name="download-the-vhd"></a>下載 VHD

下載設備的已壓縮 VHD 範本。

1. 在 [移轉目標]   > [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [探索]  。
2. 在 [探索機器]   > [機器是否已虛擬化?]  中，按一下 [是，使用 Hyper-V]  。
3. 按一下 [下載]  以下載 VHD 檔案。

    ![下載 VM](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>確認安全性

請先確認 ZIP 檔案安全無虞再進行部署。

1. 在存放下載檔案的目標電腦上，開啟系統管理員命令視窗。

2. 執行下列 PowerShell 命令以產生 ZIP 檔的雜湊
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - 使用方式範例：```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  若為 2.19.07.30 版的設備，產生的雜湊應符合這些設定。

  **演算法** | **雜湊值**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>建立設備 VM

匯入所下載的檔案，並建立 VM。

1. 將壓縮的 VHD 檔案下載至要放置設備 VM 的 Hyper-V 主機之後，請將 ZIP 檔案解壓縮。
    - 在解壓縮的位置中，檔案會解壓縮到名為 **AzureMigrateAppliance_VersionNumber** 的資料夾中。
    - 此資料夾包含子資料夾，其名稱也是 **AzureMigrateAppliance_VersionNumber**。
    - 這個子資料夾也包含三個子資料夾 - **快照集**、**虛擬硬碟**和**虛擬機器**。

2. 開啟 Hyper-V 管理員。 在 [動作]  中，按一下 [匯入虛擬機器]  。

    ![部署 VHD](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. 在 [匯入虛擬機器精靈] > [開始之前]  ，按 [下一步]  。
3. 在 [尋找資料夾]  中，選取 [虛擬機器]  資料夾。 然後按一下 [下一步]  。
1. 在 [選取虛擬機器]  中，按 [下一步]  。
2. 在 [選擇匯入類型]  中，按一下 [複製虛擬機器 (建立新的唯一識別碼)]  。 然後按一下 [下一步]  。
3. 在 [選擇目的地]  中，保留預設設定。 按 [下一步]  。
4. 在 [儲存資料夾]  中，保留預設設定。 按 [下一步]  。
5. 在 [選擇網路]  中，指定 VM 會使用的虛擬交換器。 此交換器必須能夠連線到網際網路，以將資料傳送至 Azure。
6. 在 [摘要]  中檢閱設定。 然後按一下 [ **完成**]。
7. 在 [Hyper-V 管理員] > [虛擬機器]  中，啟動 VM。


### <a name="verify-appliance-access-to-azure"></a>確認設備是否能存取 Azure

請確定設備 VM 可以連線至 [Azure URL](migrate-appliance.md#url-access)。

### <a name="configure-the-appliance"></a>設定設備

第一次設定設備。

1. 在 [Hyper-V 管理員] > [虛擬機器]  中，以滑鼠右鍵按一下 [VM] > [連線]  。
2. 提供設備的語言、時區和密碼。
3. 在任何可連線至 VM 的機器上開啟瀏覽器，並開啟設備 Web 應用程式的 URL：**https://設備名稱或 IP 位址  :44368**。

   或者，您也可以按一下應用程式捷徑，從設備桌面開啟應用程式。
1. 在 [Web 應用程式] > [設定必要條件]  中，執行下列動作：
    - **授權**：接受授權條款，並閱讀第三方資訊。
    - **連線能力**：應用程式會確認 VM 是否能夠存取網際網路。 如果 VM 使用 Proxy：
      - 按一下 [Proxy 設定]  ，然後以 http://ProxyIPAddress 或 http://ProxyFQDN 格式指定 Proxy 位址和接聽連接埠。
      - 如果 Proxy 需要驗證，請指定認證。
      - 僅支援 HTTP Proxy。
    - **時間同步**：系統會確認時間。 設備上的時間應該與網際網路時間同步，VM 探索才能正常運作。
    - **安裝更新**：Azure Migrate 伺服器評估會確認設備是否已安裝最新的更新。

### <a name="register-the-appliance-with-azure-migrate"></a>向 Azure Migrate 註冊設備

1. 按一下 [登入]  。 如果未出現，請確定您已在瀏覽器中停用快顯封鎖程式。
2. 在新的索引標籤上，使用您的 Azure 認證登入。
    - 以您的使用者名稱和密碼登入。
    - 不支援使用 PIN 登入。
3. 成功登入後，返回 Web 應用程式。
4. 選取 Azure Migrate 專案建立所在的訂用帳戶。 然後選取專案。
5. 指定設備的名稱。 名稱應該是英數位元，且長度不超過 14 個字元。
6. 按一下 [註冊]  。


### <a name="delegate-credentials-for-smb-vhds"></a>委派 SMB VHD 的認證

如果您要在 SMB 上執行 VHD，就必須將認證從設備委派到 Hyper-V 主機。 這需要下列各項：

- 您可以讓每部主機成為設備的委派。 如果您依序進行教學課程，當您準備好 Hyper-V 以進行評估和移轉時，您應該已在上一個教學課程中完成這項作業。 您應該以[手動方式](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)或藉由[執行相關指令碼](tutorial-prepare-hyper-v.md#prepare-with-a-script)，來為主機設定 CredSSP。
- 啟用 CredSSP 委派，讓 Azure Migrate 設備可作為用戶端，並將認證委派給主機。

在設備上啟用，如下所示：

#### <a name="option-1"></a>選項 1

在設備 VM 上，執行此命令。 HyperVHost1/HyperVHost2 是主機名稱範例。

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

範例： ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>選項 2

或者，也可以在設備上的本機群組原則編輯器中執行此動作：

1. 在 [本機電腦原則]   > [電腦設定]  中，按一下 [系統管理範本]   > [系統]   > [認證委派]  。
2. 按兩下 [允許委派全新認證]  ，然後選取 [已啟用]  。
3. 在 [選項]  中，按一下 [顯示]  ，然後以 **wsman/** 作為前置詞將您想要探索的每一部 Hyper-V 主機新增至清單中。
4. 然後，在 [認證委派]  中，按兩下 [允許委派僅具有 NTLM 伺服器驗證的全新認證]  。 再次以 **wsman/** 作為前置詞將您想要探索的每一部 Hyper-V 主機新增至清單中。

## <a name="start-continuous-discovery"></a>開始連續探索

從設備連線至 Hyper-V 主機或叢集，然後開始探索 VM。

1. 在 [使用者名稱]  和 [密碼]  中，指定設備會用來探索 VM 的帳戶認證。 為認證指定自訂名稱，然後按一下 [儲存詳細資料]  。
2. 按一下 [新增主機]  ，然後指定 Hyper-V 主機/叢集詳細資料。
3. 按一下 **[驗證]** 。 通過驗證後，系統就會顯示每個主機/叢集上可探索的 VM 數目。
    - 如果主機驗證失敗，請將滑鼠停留在 [狀態  ] 資料行中的圖示上，以檢閱錯誤。 修正問題，然後再次驗證。
    - 若要移除主機或叢集，請選取 [刪除]  。
    - 您無法從叢集中移除特定主機。 您只能移除整個叢集。
    - 即使叢集中的特定主機有問題，您還是可以新增叢集。
4. 驗證之後，按一下 [儲存並開始探索]  來開始探索程序。

這會開始探索。 每部主機大約耗用 1.5 分鐘，讓所探索到伺服器的中繼資料出現在 Azure 入口網站中。

### <a name="verify-vms-in-the-portal"></a>在入口網站中確認 VM

探索完成後，便可以確認 VM 是否出現在入口網站中。

1. 開啟 Azure Migrate 儀表板。
2. 在 Azure Migrate - 伺服器   >  **Azure Migrate：伺服器評量** 頁面中，按一下圖示以顯示 探索到的伺服器  計數。

## <a name="set-up-an-assessment"></a>設定評估

您可以使用 Azure Migrate 伺服器評估來執行的評估有兩種。

**評量** | **詳細資料** | **Data**
--- | --- | ---
**以效能為基礎** | 以所收集效能資料為基礎的評估 | **建議的 VM 大小**：以 CPU 和記憶體使用量資料為基礎。<br/><br/> **建議的磁碟類型 (標準或進階受控磁碟**)：以內部部署磁碟的 IOPS 和輸送量為基礎。
**作為內部部署** | 以內部部署大小調整為基礎的評估。 | **建議的 VM 大小**：以內部部署 VM 大小為基礎<br/><br> **建議的磁碟類型**：以您為評估選取的儲存體類型設定為基礎。



### <a name="run-an-assessment"></a>執行評估

執行評估，如下所示：

1. 檢閱適用於建立評估的[最佳做法](best-practices-assessment.md)。
2. 在 [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [評估]  。

    ![評定](./media/tutorial-assess-hyper-v/assess.png)

3. 在 [評估伺服器]  中，指定評估的名稱。
4. 按一下 [檢視全部]  來檢閱評估屬性。

    ![評量屬性](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. 在 [選取或建立群組]  中，選取 [新建]  ，然後指定群組名稱。 群組會將一或多個 VM 收集在一起以進行評估。
4. 在 [將機器新增至群組]  中，選取要新增至群組的 VM。
5. 按一下 [建立評估]  以建立群組，然後執行評估。

    ![建立評估](./media/tutorial-assess-hyper-v/assessment-create.png)

6. 評量建立好之後，可在 伺服器   > **Azure Migrate：** 。
7. 按一下 [匯出評估]  ，將其下載為 Excel 檔案。


## <a name="review-an-assessment"></a>檢閱評量

評量會說明：

- **Azure 移轉整備程度**：VM 是否適合移轉至 Azure。
- **每月成本預估**：在 Azure 中執行 VM 的預估每月計算和儲存體成本。
- **每月儲存體成本預估**：移轉之後的磁碟儲存體預估成本。


### <a name="view-an-assessment"></a>檢視評估

1. 在 [移轉目標]   >  [伺服器]   >  **[Azure Migrate：伺服器評量]** 中，按一下 [評量]  。
2. 在 [評量]  中，按一下評量來加以開啟。

    ![評量摘要](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>檢閱 Azure 移轉整備程度

1. 在 [Azure 移轉整備程度]  中，確認 VM 是否已準備好移轉至 Azure。
2. 檢查 VM 狀態：
    - **可供 Azure 使用**：Azure Migrate 會針對評估中的 VM 建議 VM大小和成本估計值。
    - **有條件的備妥**：顯示問題和建議的補救措施。
    - **未備妥供 Azure 使用**：顯示問題和建議的補救措施。
    - **移轉整備程度未知**：當 Azure Migrate 因資料可用性問題而無法評估整備程度時，便會使用此狀態。

2. 按一下 [Azure 移轉整備程度]  狀態。 您可以檢視 VM 的整備程度詳細資料，並向下切入以查看 VM 詳細資料，包括計算、儲存體和網路設定。

### <a name="review-cost-details"></a>檢閱成本詳細資料

此檢視會顯示在 Azure 中執行 VM 的計算和儲存體預估成本。

1. 檢閱每月的計算和儲存體成本。 系統會針對評估群組中的所有 VM 匯總成本。

    - 系統會根據機器的大小建議和其磁碟與屬性來預估成本。
    - 系統會顯示計算和儲存體的每月預估成本。
    - 以 IaaS VM 的形式來執行內部部署 VM 時，才能估計成本。 Azure Migrate 伺服器評估不會考慮 PaaS 或 SaaS 的成本。

2. 您可以檢閱每月的儲存體成本估計值。 此檢視會顯示評估群組的彙總儲存體成本，並分割為不同類型的儲存體磁碟。
3. 您可以向下切入以查看特定 VM 的詳細資料。


### <a name="review-confidence-rating"></a>檢閱信賴評等

當您執行以效能為基礎的評估時，系統會對評估指派信賴評等。

![信賴評等](./media/tutorial-assess-hyper-v/confidence-rating.png)

- 會給予 1 星 (最低) 到 5 星 (最高) 的評等。
- 信賴評等可協助您預估評估作業所提供的大小建議是否可靠。
- 信賴評等會以計算評估所需的資料點可用性為基礎。

評估的信賴評等如下所示。

**資料點可用性** | **信賴評等**
--- | ---
0%-20% | 1 顆星
21%-40% | 2 顆星
41%-60% | 3 顆星
61%-80% | 4 顆星
81%-100% | 5 顆星

[深入了解](best-practices-assessment.md#best-practices-for-confidence-ratings)信賴評等的最佳做法。





## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 已設定 Azure Migrate 設備
> * 已建立和檢閱評估

請繼續進行本系列的第三個教學課程，以了解如何使用 Azure Migrate 伺服器移轉將 Hyper-V VM 遷移至 Azure。

> [!div class="nextstepaction"]
> [遷移 Hyper-V VM](./tutorial-migrate-hyper-v.md)
