---
title: 將 Azure 自動化狀態設定報告資料轉送至 Azure 監視器記錄
description: 本文示範如何將 Desired State Configuration （DSC）報告資料從 Azure 自動化狀態設定傳送到 Azure 監視器記錄，以提供額外的深入解析和管理。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9fa84b5e87581fad4a7ada5fda074429409d2f8f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850341"
---
# <a name="forward-azure-automation-state-configuration-reporting-data-to-azure-monitor-logs"></a>將 Azure 自動化狀態設定報告資料轉送至 Azure 監視器記錄

Azure 自動化狀態設定會保留節點狀態資料30天。
如果您想要將此資料保留一段較長的時間，您可以將節點狀態資料傳送至 Log Analytics 工作區。
節點以及節點組態中個別 DSC 資源的合規性狀態會顯示在 Azure 入口網站中或使用 PowerShell 顯示。
有了 Azure 監視器記錄，您可以：

- 取得受控節點與個別資源的合規性資訊
- 根據合規性狀態觸發電子郵件或警示
- 撰寫受控節點之間的進階查詢
- 相互關聯自動化帳戶之間的合規性狀態
- 以視覺化方式呈現一段時間的合規性歷程記錄

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>必要條件

若要開始將您的自動化狀態設定報告傳送至 Azure 監視器記錄，您需要：

- 2016 年 11 月或更新版本的 [Azure PowerShell](/powershell/azure/overview) (v2.3.0)。
- Azure 自動化帳戶。 如需詳細資訊，請參閱[開始使用 Azure 自動化](automation-offering-get-started.md)。
- 提供 [自動化與控制] 服務供應項目的 Log Analytics 工作區。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](../log-analytics/log-analytics-get-started.md)。
- 至少一個 Azure Automation State Configuration 節點。 如需詳細資訊，請參閱[將機器上架交由 Azure Automation State Configuration 管理](automation-dsc-onboarding.md)
- [XDscDiagnostics](https://www.powershellgallery.com/packages/xDscDiagnostics/2.7.0.0)模組，版本2.7.0.0 或更高。 如需安裝步驟，請參閱在[您的節點上查看 DSC 記錄](./troubleshoot/desired-state-configuration.md#steps-to-troubleshoot-desired-state-configuration-dsc)。

## <a name="set-up-integration-with-azure-monitor-logs"></a>設定與 Azure 監視器記錄的整合

若要開始將資料從 Azure 自動化 DSC 匯入 Azure 監視器記錄檔中，請完成下列步驟：

1. 在 PowerShell 中登入您的 Azure 帳戶。 請參閱[使用 Azure PowerShell 登入](https://docs.microsoft.com/powershell/azure/authenticate-azureps)
1. 執行下列 PowerShell 命令以取得自動化帳戶的 _ResourceId_：(如有多個自動化帳戶，請選擇您想要設定的帳戶 _ResourceID_)。

   ```powershell
   # Find the ResourceId for the Automation Account
   Get-AzResource -ResourceType 'Microsoft.Automation/automationAccounts'
   ```

1. 執行下列 PowerShell 命令以取得 Log Analytics 工作區的 _ResourceId_：(如有多個工作區，請選擇您想要設定的工作區 _ResourceID_)。

   ```powershell
   # Find the ResourceId for the Log Analytics workspace
   Get-AzResource -ResourceType 'Microsoft.OperationalInsights/workspaces'
   ```

1. 執行下列 PowerShell 命令，以前述各步驟的 _ResourceId_ 值取代 `<AutomationResourceId>` 和 `<WorkspaceResourceId>`：

   ```powershell
   Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Category 'DscNodeStatus'
   ```

如果您想要停止將資料從 Azure 自動化狀態設定匯入 Azure 監視器記錄檔，請執行下列 PowerShell 命令：

```powershell
Set-AzDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Category 'DscNodeStatus'
```

## <a name="view-the-state-configuration-logs"></a>檢視 State Configuration 記錄

在您設定與自動化狀態設定資料的 Azure 監視器記錄整合之後，[**記錄搜尋**] 按鈕會出現在自動化帳戶的 [ **DSC 節點**] 分頁上。 按一下 [記錄搜尋] 按鈕以檢視 DSC 節點資料的記錄。

![記錄搜尋按鈕](media/automation-dsc-diagnostics/log-search-button.png)

[記錄搜尋] 刀鋒視窗隨即開啟，而且您會在套用到該節點的節點組態中，看到每個 State Configuration 節點呼叫 **DscNodeStatusData** 作業，和每個 [DSC 資源](/powershell/scripting/dsc/resources/resources)呼叫 **DscResourceStatusData** 作業。

**DscResourceStatusData** 作業包含所有失敗 DSC 資源的錯誤資訊。

按一下清單中的每項作業可查看該作業的資料。

您也可以藉由搜尋 Azure 監視器記錄來查看記錄。
請參閱[使用記錄搜尋尋找資料](../log-analytics/log-analytics-log-searches.md)。
鍵入下列查詢來尋找 State Configuration 記錄：`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus'`

您也可以依作業名稱縮小查詢範圍。 例如：`Type=AzureDiagnostics ResourceProvider='MICROSOFT.AUTOMATION' Category='DscNodeStatus' OperationName='DscNodeStatusData'`

### <a name="send-an-email-when-a-state-configuration-compliance-check-fails"></a>State Configuration 合規性檢查失敗時傳送電子郵件

我們最常從客戶收到的其中一個問題，便是希望系統能在 DSC 設定發生問題時，傳送電子郵件或簡訊通知他們。

若要建立警示規則，首先針對應叫用警示的 State Configuration 報告記錄，建立記錄檔搜尋。 按一下 [+ 新增警示規則] 按鈕，以建立並設定警示規則。

1. 從 Log Analytics 工作區的 [總覽] 頁面，按一下 [**記錄**]。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' NodeName_s='DSCTEST1' OperationName='DscNodeStatusData' ResultType='Failed'`。

   如果您已將來自多個自動化帳戶或訂用帳戶的記錄設定到您的工作區，就能依訂用帳戶或自動化帳戶來將警示分組。
   自動化帳戶名稱可以衍生自 DscNodeStatusData 搜尋的 [資源] 欄位。
1. 若要開啟 [建立規則] 畫面，按一下頁面頂端的 [+ 新增警示規則]。 如需設定警示選項的詳細資訊，請參閱[建立警示規則](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)。

### <a name="find-failed-dsc-resources-across-all-nodes"></a>在所有節點間尋找失敗的 DSC 資源

使用 Azure 監視器記錄的優點之一，是您可以在節點之間搜尋失敗的檢查。
尋找所有失敗的 DSC 資源執行個體。

1. 從 Log Analytics 工作區的 [總覽] 頁面，按一下 [**記錄**]。
1. 在查詢欄位中鍵入下列搜尋內容來為您的警示建立記錄搜尋查詢：`Type=AzureDiagnostics Category='DscNodeStatus' OperationName='DscResourceStatusData' ResultType='Failed'`。

### <a name="view-historical-dsc-node-status"></a>檢視歷程記錄 DSC 節點狀態

最後，您也許想要以視覺化方式呈現一段時間的 DSC 節點狀態歷程記錄。
您可以使用此查詢來搜尋 DSC 節點狀態一段時間後的狀態。

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`

這會顯示一段時間的節點狀態圖表。

## <a name="azure-monitor-logs-records"></a>Azure 監視器記錄檔記錄

來自 Azure 自動化的診斷會在 Azure 監視器記錄檔中建立兩種記錄類別。

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscNodeStatusData |
| ResultType |節點是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| NodeComplianceStatus_s |節點是否符合規範。 |
| DscReportStatus |合規性檢查是否已順利執行。 |
| ConfigurationMode | 設定如何套用至節點。 可能的值為 __"ApplyOnly"__ 、 __"ApplyandMonitior"__ 和 __"ApplyandAutoCorrect"__ 。 <ul><li>__ApplyOnly__：DSC 會套用設定但不執行任何進一步的動作，除非有新的設定發送到目標節點，或從伺服器提取新的設定時。 初始套用新的設定之後，DSC 不會檢查先前設定的狀態是否漂移。 DSC 在 __ApplyOnly__ 生效之前會一直嘗試套用設定，直到成功為止。 </li><li> __ApplyAndMonitor__：這是預設值。 LCM 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異。 DSC 在 __ApplyAndMonitor__ 生效之前會一直嘗試套用設定，直到成功為止。</li><li>__ApplyAndAutoCorrect__：DSC 會套用任何新的設定。 初始套用新設定之後，如果目標節點從所需狀態漂移，DSC 會在記錄中報告差異，然後重新套用目前的設定。</li></ul> |
| HostName_s | 受控節點名稱。 |
| IPAddress | 受控節點的 IPv4 位址。 |
| 類別 | DscNodeStatus |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| LastSeenTime_t |上一次檢視報表的日期和時間。 |
| ReportStartTime_t |報表開始的日期和時間。 |
| ReportEndTime_t |報表完成的日期和時間。 |
| NumberOfResources_d |在節點套用的設定中呼叫的 DSC 資源數目。 |
| SourceSystem | Azure 監視器記錄檔收集資料的方式。 針對 Azure 診斷，一律為 Azure 。 |
| ResourceId |指定 Azure 自動化帳戶。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |為更新狀態報告之相互關聯識別碼的 GUID。 |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| 屬性 | 描述 |
| --- | --- |
| TimeGenerated |執行合規性檢查的日期和時間。 |
| OperationName |DscResourceStatusData|
| ResultType |資源是否符合規範。 |
| NodeName_s |受控節點名稱。 |
| 類別 | DscNodeStatus |
| 資源 | Azure 自動化帳戶的名稱。 |
| Tenant_g | 識別呼叫端租用戶的 GUID。 |
| NodeId_g |識別受控節點的 GUID。 |
| DscReportId_g |識別報表的 GUID。 |
| DscResourceId_s |DSC 資源執行個體的名稱。 |
| DscResourceName_s |DSC 資源的名稱。 |
| DscResourceStatus_s |DSC 資源是否符合規範。 |
| DscModuleName_s |包含 DSC 資源的 PowerShell 模組名稱。 |
| DscModuleVersion_s |包含 DSC 資源的 PowerShell 模組版本。 |
| DscConfigurationName_s |節點套用的設定名稱。 |
| ErrorCode_s | 資源失敗時的錯誤代碼。 |
| ErrorMessage_s |資源失敗時的錯誤訊息。 |
| DscResourceDuration_d |DSC 資源執行的時間，以秒為單位。 |
| SourceSystem | Azure 監視器記錄檔收集資料的方式。 針對 Azure 診斷，一律為 Azure 。 |
| ResourceId |指定 Azure 自動化帳戶。 |
| ResultDescription | 此作業的描述。 |
| SubscriptionId | 自動化帳戶的 Azure 訂用帳戶識別碼 (GUID)。 |
| ResourceGroup | 自動化帳戶的資源群組名稱。 |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |為更新狀態報告之相互關聯識別碼的 GUID。 |

## <a name="summary"></a>總結

藉由將您的自動化狀態設定資料傳送至 Azure 監視器記錄，您可以藉由下列方式，更深入瞭解自動化狀態設定節點的狀態：

- 設定警示，在發生問題時通知您
- 使用自訂檢視和搜尋查詢，以視覺化方式檢視您的 Runbook 結果、Runbook 作業狀態，以及其他相關的關鍵指標或計量。

Azure 監視器記錄可為您的自動化狀態設定資料提供更高的操作可見度，並有助於更快速地解決事件。

## <a name="next-steps"></a>後續步驟

- 如需概觀，請參閱 [Azure 自動化狀態設定](automation-dsc-overview.md)
- 若要開始使用，請參閱[開始使用 Azure 自動化狀態設定](automation-dsc-getting-started.md)
- 若要了解如何編譯 DSC 組態，以將它們指派給目標節點，請參閱[編譯 Azure Automation State Configuration 中的組態](automation-dsc-compile.md)
- 如需 PowerShell Cmdlet 參考，請參閱 [Azure 自動化狀態設定 Cmdlet](/powershell/module/azurerm.automation/#automation)
- 如需定價資訊，請參閱 [Azure 自動化狀態設定的定價](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持續部署管線中使用 Azure 自動化狀態設定的範例，請參閱[使用 Azure 自動化狀態設定和 Chocolatey 的持續部署](automation-dsc-cd-chocolatey.md)
- 若要深入瞭解如何使用 Azure 監視器記錄來建立不同的搜尋查詢，並查看自動化狀態設定記錄，請參閱[Azure 監視器記錄檔中的記錄搜尋](../log-analytics/log-analytics-log-searches.md)
- 若要深入瞭解 Azure 監視器記錄和資料收集來源，請參閱[在 Azure 監視器記錄中收集 Azure 儲存體資料總覽](../azure-monitor/platform/collect-azure-metrics-logs.md)
