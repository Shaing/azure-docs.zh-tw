---
title: 教學課程 - 將 SAP HANA 資料庫備份至 Azure VM
description: 在本教學課程中，您將了解如何將執行於 Azure VM 上的 SAP HANA 資料庫備份至 Azure 備份復原服務保存庫。
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: bb84f6b362adf7c190f3300e6e3f1bc572153151
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753987"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>教學課程：將 SAP HANA 資料庫備份至 Azure VM

本教學課程說明如何將執行於 Azure VM 上的 SAP HANA 資料庫備份至 Azure 備份復原服務保存庫。 在本文中，您將了解如何：

> [!div class="checklist"]
>
> * 建立和設定保存庫
> * 探索資料庫
> * 設定備份

[這裡](sap-hana-backup-support-matrix.md#scenario-support)提供我們目前支援的所有案例。

## <a name="onboard-to-the-public-preview"></a>上線至公開預覽

上線至公開預覽，如下所示：

* 在入口網站中，[依照本文的指示](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)將您的訂用帳戶識別碼註冊至「復原服務」服務提供者。

* 在 PowerShell 中，請執行下列 Cmdlet。 其結果應為「已註冊」。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="prerequisites"></a>Prerequisites

設定備份之前，請務必先執行下列動作：

1. 在執行 SAP HANA 資料庫的 VM 上，使用 zypper 從官方 SLES 套件/媒體安裝並啟用 ODBC 驅動程式套件，如下所示：

```bash
sudo zypper update
sudo zypper install unixODBC
```

>[!NOTE]
> 如果您未更新存放庫，請確定 unixODBC 的版本至少是 2.3.4。 若要確認 uniXODBC 的版本，請以根使用者身分執行 `odbcinst -j`
>

2. 允許從 VM 到網際網路的連線，使其可以觸達 Azure，如[下列程序](#set-up-network-connectivity)所說明。

3. 以根使用者身分，在安裝 HANA 的虛擬機器中執行預先註冊指令碼。 [此指令碼](https://aka.ms/scriptforpermsonhana)會設定[正確權限](#setting-up-permissions)。

## <a name="set-up-network-connectivity"></a>設定網路連線

在執行所有作業時，SAP HANA VM 都需要連線至 Azure 公用 IP 位址。 若未連線至 Azure 公用 IP 位址，VM 作業 (資料庫探索、設定備份、排程備份、還原復原點等) 將會失敗。

請使用下列其中一個選項來建立連線：

### <a name="allow-the-azure-datacenter-ip-ranges"></a>允許 Azure 資料中心 IP 範圍

此選項會允許所下載檔案中的 [IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。 若要存取網路安全性群組 (NSG)，請使用 Set-AzureNetworkSecurityRule Cmdlet。 如果安全收件者清單只包含區域專屬的 IP，您還必須更新安全收件者清單 Azure Active Directory (Azure AD) 服務標籤以啟用驗證。

### <a name="allow-access-using-nsg-tags"></a>允許使用 NSG 標籤來存取

如果您使用 NSG 來限制連線，則請使用 AzureBackup 服務標籤來允許針對 Azure 備份進行輸出存取。 此外，您也應該使用 Azure AD 和 Azure 儲存體的[規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)，來允許驗證和資料傳輸使用連線能力。 這可以從 Azure 入口網站或透過 PowerShell 來進行。

若要使用入口網站建立規則：

  1. 在 [所有服務]  中，移至 [網路安全性群組]  ，然後選取網路安全性群組。
  2. 選取 [設定]  底下的 [輸出安全性規則]  。
  3. 選取 [新增]  。 輸入可供用於建立新規則的所有必要詳細資料，如[安全性規則設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)中所述。 請確定 [目的地]  選項已設定為 [服務標籤]  ，且 [目的地服務標籤]  已設定為 [AzureBackup]  。
  4. 按一下 [新增]  以儲存新建立的輸出安全性規則。

若要使用 PowerShell 建立規則：

 1. 新增 Azure 帳戶認證並更新國家雲端<br/>
      `Add-AzureRmAccount`<br/>

 2. 選取 NSG 訂用帳戶<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. 選取 NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. 新增 Azure 備份服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. 新增儲存體服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. 新增 AzureActiveDirectory 服務標籤的允許輸出規則<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. 儲存 NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**允許使用 Azure 防火牆標籤來存取**。 如果您使用的是 Azure 防火牆，請使用 AzureBackup [FQDN 標籤](https://docs.microsoft.com/azure/firewall/fqdn-tags)來建立應用程式規則。 這會允許針對 Azure 備份進行輸出存取。

**部署 HTTP Proxy 伺服器以路由流量**。 當您在 Azure VM 上備份 SAP HANA 資料庫時，VM 上的備份擴充功能會使用 HTTPS API 將管理命令傳送至 Azure 備份，並將資料傳送至 Azure 儲存體。 備份延伸模組也會使用 Azure AD 進行驗證。 透過 HTTP Proxy 路由傳送這三項服務的備份延伸模組流量。 延伸模組是唯一為了要存取公用網際網路而設定的元件。

連線能力選項包含下列優缺點：

**選項** | **優點** | **缺點**
--- | --- | ---
允許 IP 範圍 | 沒有額外的成本 | 由於 IP 位址範圍會隨著時間改變，因此難以管理 <br/><br/> 提供整個 Azure 的存取權，而不只是 Azure 儲存體的存取權
使用 NSG 服務標籤 | 會自動合併範圍變更，因此更容易管理 <br/><br/> 沒有額外的成本 <br/><br/> | 只能搭配 NSG 使用 <br/><br/> 提供整個服務的存取權
使用 Azure 防火牆 FQDN 標籤 | 會自動管理所需的 FQDN，因此更容易管理 | 只能搭配 Azure 防火牆使用
使用 HTTP Proxy | 允許在 Proxy 中精確控制儲存體 URL <br/><br/> VM 的單一網際網路存取點 <br/><br/> 不會隨著 Azure IP 位址變更 | 使用 Proxy 軟體執行 VM 時的額外成本

## <a name="setting-up-permissions"></a>設定權限

預先註冊指令碼會執行下列動作：

1. 在 HANA 系統中建立 AZUREWLBACKUPHANAUSER，並新增下列必要的角色和權限：
   * 資料庫管理員：在還原期間建立新的資料庫。
   * 目錄讀取：讀取備份目錄。
   * SAP_INTERNAL_HANA_SUPPORT：存取數個私人資料表。
2. 將金鑰新增至 Hdbuserstore，供 HANA 外掛程式處理所有作業 (資料庫查詢、還原作業、設定和執行備份)。

若要確認金鑰是否已建立，請使用 SIDADM 認證在 HANA 電腦上執行 HDBSQL 命令：

```hdbsql
hdbuserstore list
```

命令輸出應該會顯示 {SID}{DBNAME} 金鑰，且使用者會顯示為 AZUREWLBACKUPHANAUSER。

>[!NOTE]
> 請確定您在 /usr/sap/{SID}/home/.hdb/ 下有一組唯一的 SSFS 檔案。 此路徑中應該只有一個資料夾。
>

## <a name="create-a-recovery-service-vault"></a>建立復原服務保存庫

復原服務保存庫是一個實體，可儲存一段時間以來建立的備份和復原點。 復原服務保存庫也包含與受保護虛擬機器相關聯的備份原則。

若要建立復原服務保存庫：

1. 在 [Azure 入口網站](https://portal.azure.com/)中登入您的訂用帳戶。

2. 在左側功能表上，選取 [所有服務] 

![選取所有服務](./media/tutorial-backup-sap-hana-db/all-services.png)

3. 在 [所有服務]  對話方塊中，輸入 **Recovery Services**。 資源清單會根據您的輸入進行篩選。 在資源清單中，選取 [復原服務保存庫]  。

![選取 [復原服務保存庫]](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. 在 [復原服務保存庫]  儀表板上，選取 [新增]  。

![新增復原服務保存庫](./media/tutorial-backup-sap-hana-db/add-vault.png)

[復原服務保存庫]  對話方塊隨即開啟。 提供 [名稱]、[訂用帳戶]、[資源群組]  和 [位置]  的值

![建立復原服務保存庫](./media/tutorial-backup-sap-hana-db/create-vault.png)

* **Name**：名稱會用來識別復原服務保存庫，且對於 Azure 訂用帳戶必須是唯一的。 指定的名稱至少要有兩個字元，但不能超過 50 個字元。 名稱開頭必須是字母，且只能包含字母、數字和連字號。 在本教學課程中，我們使用名稱 **SAPHanaVault**。
* 訂用帳戶  ：選擇要使用的訂用帳戶。 如果您是唯一一個訂用帳戶的成員，就會看到該名稱。 如果您不確定要使用哪個訂用帳戶，請使用預設 (建議) 的訂用帳戶。 只有在您的公司或學校帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。 在此，我們使用了 **SAP HANA 解決方案實驗室訂用帳戶**訂用帳戶。
* **資源群組**：使用現有的資源群組或建立新群組。 在此，我們使用了 **SAPHANADemo**。<br>
若要查看您訂用帳戶中可用的資源群組清單，請選取 [使用現有的]  ﹐然後從下拉式清單方塊中選取資源。 若要建立新的資源群組，請選取 [新建]  ，然後輸入名稱。 如需資源群組的完整資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
* **位置**：選取保存庫的地理區域。 保存庫必須與執行 SAP HANA 的虛擬機器位於相同的區域中。 我們使用**美國東部 2**。

5. 選取 [檢閱 + 建立]  。

   ![選取 [檢閱並建立]](./media/tutorial-backup-sap-hana-db/review-create.png)

此時會建立復原服務保存庫。

## <a name="discover-the-databases"></a>探索資料庫

1. 在保存庫的 [使用者入門]  中，按一下 [備份]  。 在 [工作負載的執行位置？]  中，選取 [Azure VM 中的 SAP HANA]  。
2. 按一下 [開始探索]  。 這會對保存庫區域中未受保護的 Linux VM 起始探索。 您會看到您想要保護的 Azure VM。
3. 在 [選取虛擬機器]  中按一下連結，以下載提供權限讓 Azure 備份服務存取 SAP Hana VM 以進行資料庫探索的指令碼。
4. 在您要備份的 SAP HANA 資料庫裝載所在的 VM 上執行指令碼。
5. 在 VM 上執行指令碼後，在 [選取虛擬機器]  中選取 VM。 然後，按一下 [探索資料庫]  。
6. Azure 備份會探索 VM 上的所有 SAP HANA 資料庫。 在探索期間，Azure 備份會向保存庫註冊 VM，並在 VM 上安裝延伸模組。 資料庫上不會安裝代理程式。

   ![探索資料庫](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>設定備份

在探索到要備份的資料庫後，接下來我們將啟用備份。

1. 按一下 [設定備份]  。

![設定備份](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. 在 [選取要備份的項目]  中，選取一或多個要保護的資料庫，然後按一下 [確定]  。

![選取要備份的項目](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. 在 [備份原則] > [選擇備份原則]  中，根據下一節中的指示，為資料庫建立新的備份原則。

![選擇備份原則](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. 在建立原則後，在 [備份]  功能表上按一下 [啟用備份]  。

   ![按一下 [啟用備份]](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. 在入口網站的 [通知]  區域中，追蹤備份設定進度。

## <a name="creating-a-backup-policy"></a>建立備份原則

備份原則會定義備份的進行時間和備份的保留時間長度。

* 原則會建立於保存庫層級上。
* 多個保存庫可以使用相同的備份原則，但您必須將備份原則套用至每個保存庫。

指定原則設定，如下所示：

1. 在 [原則名稱]  中，為新原則輸入名稱。 在此案例中，請輸入 **SAPHANA**。

![輸入新原則的名稱](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. 在 [完整備份原則]  中，選取 [備份頻率]  。 您可以選擇 [每日]  或 [每週]  。 在本教學課程中，我們選擇 [每日]  備份。

![選取備份頻率](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. 在 [保留範圍]  中，設定完整備份的保留期設定。
   * 依預設會選取所有選項。 請清除您不想要使用的任何保留範圍限制，並設定您要使用的限制。
   * 所有備份類型 (完整/差異/記錄) 的最小保留期間皆為七天。
   * 復原點會根據其保留範圍標記為保留。 例如，如果您選取每日完整備份，每天只會觸發一次完整備份。
   * 系統會根據每週保留範圍和設定，標記和保留特定日期的備份。
   * 每月和每年保留範圍會以類似方式運作。
4. 在 [完整備份原則]  功能表中，按一下 [確定]  以接受設定。
5. 然後，選取 [差異備份]  以新增差異原則。
6. 在 [差異備份原則]  中，選取 [啟用]  以開啟頻率和保留控制項。 我們已啟用會在每**週日**的**凌晨 2:00** 執行的差異備份，此備份會保留 **30 天**。

   ![差異備份原則](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

>[!NOTE]
>目前不支援增量備份。
>

7. 按一下 [確定]  以儲存原則，然後返回主要 [備份原則]  功能表。
8. 選取 [記錄備份]  ，以新增交易記錄備份原則。
   * [記錄備份]  會預設為 [啟用]  。 此選項無法停用，因為 SAP HANA 管理所有的記錄備份。
   * 我們將備份排程設為 **2 小時**，並將保留期間設為 **15 天**。

    ![記錄備份原則](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

>[!NOTE]
> 只有在順利完成一個完整備份後，記錄備份才會開始運作。
>

9. 按一下 [確定]  以儲存原則，然後返回主要 [備份原則]  功能表。
10. 在完成備份原則的定義後，按一下 [確定]  。

現在，您已成功設定 SAP HANA 資料庫的備份。

## <a name="next-steps"></a>後續步驟

* 了解如何[在執行於 Azure VM 的 SAP HANA 資料庫上執行隨選備份](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* 了解如何[還原執行於 Azure VM 上的 SAP HANA 資料庫](sap-hana-db-restore.md)
* 了解如何[管理使用 Azure 備份進行備份的 SAP HANA 資料庫](sap-hana-db-manage.md)
* 了解如何[對 SAP HANA 資料庫備份時的常見問題進行疑難排解](backup-azure-sap-hana-database-troubleshoot.md)
