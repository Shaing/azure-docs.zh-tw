---
title: 在 Azure VM 上使用受控識別搭配 Azure Sdk-Azure AD
description: 搭配具有 Azure 資源受控識別的 Azure VM 使用 Azure SDK 的程式碼範例。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184159"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>如何搭配 Azure SDK 使用 Azure VM 的 Azure 資源受控識別 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
本文提供一份 SDK 範例清單，其中示範如何針對 Azure 資源的受控識別使用其各自的 Azure SDK 支援。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 Azure 資源受控識別的虛擬機器上執行。 在 Azure 入口網站中使用 VM「連線」功能，從遠端連線到您的 VM。 如需有關在虛擬機器上啟用 Azure 資源受控識別的詳細資訊，請參閱[使用 Azure 入口網站在虛擬機器上設定 Azure 資源的受控識別](qs-configure-portal-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、CLI、範本或 Azure SDK)。 

## <a name="sdk-code-samples"></a>SDK 程式碼範例

| SDK             | 程式碼範例 |
| --------------- | ----------- |
| .NET            | [使用 Azure 資源的受控識別從 Windows VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [使用 Azure 資源的受控識別從 Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [使用 Azure 資源的受控識別管理資源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [使用 Azure 資源的受控識別直接從虛擬機器內驗證](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [從 Azure 資源的受控識別啟用的虛擬機器管理資源](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure SDK](https://azure.microsoft.com/downloads/)，以取得 Azure SDK 資源的完整清單，包括程式庫下載、文件及更多項目。
- 若要啟用 Azure VM 上的 Azure 資源的受控識別，請參閱[使用 Azure 入口網站在虛擬機器上設定 Azure 資源的受控識別](qs-configure-portal-windows-vm.md)。








