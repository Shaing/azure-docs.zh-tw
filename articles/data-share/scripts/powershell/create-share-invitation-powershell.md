---
title: PowerShell 指令碼：建立 Azure 資料共用邀請 |Microsoft Docs
description: 此 PowerShell 腳本會傳送資料共用邀請。
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307238"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>使用 PowerShell 監視已傳送資料共用的使用方式

此 PowerShell 腳本會建立資料共用邀請。

## <a name="sample-script"></a>範例指令碼


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令： 

| 命令 | 注意 |
|---|---|
| [新增-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | 建立資料共用邀請。 |
|||

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 的詳細資訊，請參閱 [Azure PowerShell 文件](https://docs.microsoft.com/powershell/)。

您可以在[Azure 資料共用 powershell 範例](../../samples-powershell.md)中找到其他 Azure 資料共用 powershell 腳本範例。
