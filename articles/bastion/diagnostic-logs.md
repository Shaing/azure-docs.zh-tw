---
title: 啟用及使用 Azure 防禦診斷記錄 |Microsoft Docs
description: 在本文中，您將瞭解如何啟用及使用 Azure 防禦診斷記錄。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 8ae421b48f3cfa8055f636052c990f99e0c775b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512401"
---
# <a name="enable-and-work-with-bastion-diagnostic-logs"></a>啟用和使用防禦診斷記錄

當使用者使用 Azure 防禦連線到工作負載時，防禦可以記錄遠端會話的診斷。 接著，您可以使用診斷來查看哪些使用者連線到哪些工作負載，以及從何處和其他這類相關的記錄資訊。 若要使用診斷，您必須在 Azure 防禦上啟用診斷記錄。 本文可協助您啟用診斷記錄，然後再查看記錄。

## <a name="enable"></a>啟用診斷記錄

1. 在  [Azure 入口網站](https://portal.azure.com)中，流覽至您的 azure 防禦資源，然後從 Azure 防禦頁面選取 **診斷設定**]。

   ![診斷設定](./media/diagnostic-logs/1diagnostics-settings.png)
2. 選取 [**診斷設定**]，然後選取 [ **+ 新增診斷設定**] 以新增記錄的目的地。

   ![新增診斷設定](./media/diagnostic-logs/2add-diagnostic-setting.png)
3. 在 [**診斷設定**] 頁面上，選取要用來儲存診斷記錄的儲存體帳戶類型。

   ![選取儲存位置](./media/diagnostic-logs/3add-storage-account.png)
4. 當您完成設定時，它看起來會類似下列範例：

   ![範例設定](./media/diagnostic-logs/4example-settings.png)

## <a name="view"></a>查看診斷記錄

若要存取您的診斷記錄，您可以直接使用您在啟用診斷設定時所指定的儲存體帳戶。

1. 導覽至您的儲存體帳戶資源，然後流覽至 [**容器**]。 您會看到在儲存體帳戶 blob 容器中建立的**insights-logs-bastionauditlogs** blob。

   ![診斷設定](./media/diagnostic-logs/1-navigate-to-logs.png)
2. 當您在容器內流覽至時，您會在您的 blog 中看到各種不同的資料夾。 這些資料夾會指出您的 Azure 防禦資源的資源階層。

   ![新增診斷設定](./media/diagnostic-logs/2-resource-h.png)
3. 流覽至您想要存取/查看其診斷記錄的 Azure 防禦資源的完整階層。 「Y =」、「等於」、「d =」、「h =」和「=」分別指出診斷記錄的年、月、日、小時和分鐘。

   ![選取儲存位置](./media/diagnostic-logs/3-resource-location.png)
4. 找出 Azure 防禦所建立的 json 檔案，其中包含所流覽之時間週期的診斷記錄資料。

5. 從您的儲存體 blob 容器下載 json 檔案。 Json 檔案中的範例專案如下所示，供您參考：

   ```json
   { 
   "time":"2019-10-03T16:03:34.776Z",
   "resourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.NETWORK/BASTIONHOSTS/MYBASTION-BASTION",
   "operationName":"Microsoft.Network/BastionHost/connect",
   "category":"BastionAuditLogs",
   "level":"Informational",
   "location":"eastus",
   "properties":{ 
      "userName":"<username>",
      "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.90 Safari/537.36",
      "clientIpAddress":"131.107.159.86",
      "clientPort":24039,
      "protocol":"ssh",
      "targetResourceId":"/SUBSCRIPTIONS/<subscripionID>/RESOURCEGROUPS/MYBASTION/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/LINUX-KEY",
      "subscriptionId":"<subscripionID>",
      "message":"Successfully Connected.",
      "resourceType":"VM",
      "targetVMIPAddress":"172.16.1.5",
      "tunnelId":"<tunnelID>"
   },
   "FluentdIngestTimestamp":"2019-10-03T16:03:34.0000000Z",
   "Region":"eastus",
   "CustomerSubscriptionId":"<subscripionID>"
   }
   ```

## <a name="next-steps"></a>後續步驟

閱讀防禦[常見問題](bastion-faq.md)。
