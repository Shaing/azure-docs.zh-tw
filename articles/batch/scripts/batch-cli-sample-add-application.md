---
title: Azure CLI 指令碼範例 - 在 Batch 中加入應用程式
description: 此範例指令碼示範如何新增應用程式，以搭配 Azure Batch 集區或工作使用。
services: batch
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: jushiman
ms.openlocfilehash: f8fb891c2ec6f531ff87b15fe12da46a9cd93941
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029440"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 範例：將應用程式新增到 Azure Batch 帳戶

此指令碼示範如何新增應用程式，以搭配 Azure Batch 集區或工作使用。 若要將應用程式設定為新增到 Batch 帳戶，請將可執行檔與任何相依項目封裝到 .zip 檔案中。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。
下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 建立用來存放所有資源的資源群組。 |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | 建立儲存體帳戶。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | 建立 Batch 帳戶。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | 建立應用程式。  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | 將應用程式封裝加入指定的應用程式。  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | 更新應用程式的屬性。  |
| [az group delete](/cli/azure/group#az-group-delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。
