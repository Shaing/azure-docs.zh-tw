---
title: 使用 PowerShell 管理 Azure 檔案共用備份
description: 瞭解如何使用 PowerShell 來管理和監視由 Azure 備份服務所備份的 Azure 檔案共用。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 9004e16c4b7c6ee704460d744612bafcd32831b9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776413"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>使用 PowerShell 管理 Azure 檔案共用備份

本文說明如何使用 Azure PowerShell 來管理和監視由 Azure 備份服務所備份的 Azure 檔案共用。

## <a name="modify-the-protection-policy"></a>修改保護原則

若要變更用來備份 Azure 檔案共用的原則，請使用[enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)。 指定相關的備份專案和新的備份原則。

下列範例會將 **testAzureFS** 保護原則從 **dailyafs** 變更為 **monthlyafs**。

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>追蹤備份和還原作業

隨選備份和還原作業會連同識別碼一起傳回工作，如您[執行隨選備份](backup-azure-afs-automation.md#trigger-an-on-demand-backup)中所示。 使用[AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) Cmdlet 來追蹤作業進度和詳細資料。

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```

## <a name="stop-protection-on-a-file-share"></a>停止保護檔案共用

有兩種方式可停止保護 Azure 檔案共用︰

* 停止所有未來的備份作業並*刪除*所有復原點
* 停止所有未來的備份工作，但*保留*復原點

在儲存體中保留復原點可能會產生相關成本，因為將會保留 Azure 備份所建立的基礎快照集。 不過，保留復原點的好處是您稍後可以視需要還原檔案共用。 如需復原點保留成本的相關資訊，請參閱 [定價詳細資料](https://azure.microsoft.com/pricing/details/storage/files/)。 如果您選擇刪除所有復原點，則無法還原檔案共用。

## <a name="stop-protection-and-retain-recovery-points"></a>停止保護並保留復原點

若要在保留資料時停止保護，請使用[enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) Cmdlet。

下列範例會停止*命名為 afsfileshare*檔案共用的保護，但會保留所有復原點：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

輸出中的作業識別碼屬性會對應至備份服務針對您的「停止保護」作業所建立之工作的作業識別碼。 若要追蹤作業的狀態，請使用[AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0) Cmdlet。

## <a name="stop-protection-without-retaining-recovery-points"></a>停止保護，而不保留復原點

若要停止保護而不保留復原點，請使用[enable-azrecoveryservicesbackupprotection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) Cmdlet 並新增 **-RemoveRecoveryPoints**參數。

下列範例會停止保護*命名為 afsfileshare*檔案共用，而不會保留復原點：

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>後續步驟

[瞭解如何](manage-afs-backup.md)在 Azure 入口網站中管理 Azure 檔案共用備份。
