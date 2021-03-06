---
title: Azure 資料箱磁碟疑難排解磁碟解除鎖定的問題 |Microsoft Docs
description: 說明如何針對在 Azure 資料箱磁碟中察覺的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148279"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>針對磁碟在 Azure 資料箱磁碟中解除鎖定的問題進行疑難排解

本文適用於 Microsoft Azure 資料箱磁碟，並說明用來疑難排解任何問題，使用解除鎖定工具時的工作流程。 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>資料箱磁碟解除鎖定工具錯誤


| 錯誤訊息/工具行為      | 建議                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| 不支援最新版本的 .NET Framework。 支援的版本是 4.5 版和更新版本。<br><br>工具結束並且有訊息。  | 未安裝 .NET 4.5。 請在執行資料箱磁碟解除鎖定工具的主機電腦上，安裝 .NET 4.5 或更新版本。                                                                            |
| 無法解除鎖定或驗證任何磁碟區。 連絡 Microsoft 支援服務。  <br><br>此工具無法解除鎖定或驗證任何已鎖定的磁碟機。 | 此工具無法使用提供的通行金鑰，解除鎖定任何已鎖定的磁碟機。 連絡 Microsoft 支援服務以進行後續步驟。                                                |
| 下列磁碟區已解除鎖定並經過驗證。 <br>磁碟區磁碟機代號：E:<br>無法使用下列通行金鑰解除鎖定任何磁碟區：werwerqomnf、qwerwerqwdfda <br><br>此工具會解除鎖定部分磁碟機，並列出成功和失敗的磁碟機代號。| 部分成功。 無法使用提供的通行金鑰解除鎖定部分磁碟機。 連絡 Microsoft 支援服務以進行後續步驟。 |
| 找不到鎖定的磁碟區。 請確認從 Microsoft 處接收的磁碟已正確連線且處於鎖定狀態。          | 此工具找不到任何鎖定的磁碟機。 可能磁碟機已解除鎖定，或未偵測到磁碟機。 請確定磁碟機已連線且已鎖定。                                                           |
| 嚴重錯誤:參數不正確<br>參數名稱：invalid_arg<br>使用方式：<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>範例：DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>範例：DataBoxDiskUnlock /SystemCheck<br>範例：DataBoxDiskUnlock /Help<br><br>/PassKeys：     從 Azure 資料箱磁碟訂單取得此通行金鑰。 通行金鑰會將您的磁碟解除鎖定。<br>/Help：         這個選項提供 Cmdlet 使用方式和範例的說明。<br>/SystemCheck：  這個選項會檢查您的系統是否符合執行工具的需求。<br><br>按任意鍵以結束。 | 輸入的參數無效。 唯一允許的參數是 /SystemCheck、 /PassKey 和 /Help。|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>使用 Windows 用戶端時，解除鎖定磁碟的問題

本節將詳細說明一些使用 Windows 用戶端進行的資料複製時，資料箱磁碟部署期間所面臨的常見問題。

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>問題：無法從 BitLocker 將磁碟機解除鎖定
 
**原因** 

您使用 BitLocker 對話方塊中的密碼，並嘗試透過 BitLocker 解除鎖定磁碟機對話方塊來解除鎖定。 這不可行。

**解決方案**

若要將資料箱磁碟解除鎖定，您需要使用「資料箱磁碟解除鎖定工具」，並提供來自 Azure 入口網站的密碼。 如需詳細資訊，請移至[教學課程：針對 Azure 資料箱磁碟解除封裝、連線然後解除鎖定](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey)。
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>問題：無法解除鎖定或驗證某些磁碟區。 連絡 Microsoft 支援服務。
 
**原因**

您可能會在錯誤記錄檔中看到下列錯誤，且無法解除鎖定或驗證某些磁碟區。

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
這表示您的 Windows 用戶端上可能沒有適當版本的 Windows PowerShell。

**解決方案**

您可以安裝 [Windows PowerShell v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) \(英文\) 並重試作業。
 
如果您仍然無法將磁碟區解除鎖定，請複製有資料箱磁碟解除鎖定工具的資料夾之中的記錄，並[連絡 Microsoft 支援服務](data-box-disk-contact-microsoft-support.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何[針對驗證問題進行疑難排解](data-box-disk-troubleshoot.md)。
