---
title: 無法使用 Key Vault 憑證來新增自訂網域
titleSuffix: Azure API Management
description: 瞭解如何使用金鑰保存庫憑證，針對無法在 Azure API 管理中新增自訂網域的問題進行疑難排解。
services: api-management
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/19/2019
ms.author: tehnoonr
ms.openlocfilehash: a09c15466a4a9f62b2696b087cb7ab23cc767379
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430583"
---
# <a name="failed-to-update-api-management-service-hostnames"></a>無法更新 API 管理服務主機名

本文說明當您新增 Azure API 管理服務的自訂網域時，可能會遇到的「無法更新 API 管理服務主機名」錯誤。 本文提供可協助您解決問題的疑難排解步驟。

## <a name="symptoms"></a>徵狀

當您嘗試使用 Azure Key Vault 的憑證，為您的 API 管理服務新增自訂網域時，您會收到下列錯誤訊息：

- 無法更新 API 管理服務主機名。 對資源 'https://vaultname.vault.azure.net/secrets/secretname/?api-version=7.0 ' 的要求失敗，StatusCode：禁止使用 RequestId：。 例外狀況訊息：作業傳回不正確狀態碼「禁止」。

## <a name="cause"></a>原因

API 管理服務沒有許可權可存取您嘗試用於自訂網域的金鑰保存庫。

## <a name="solution"></a>解決方案

若要解決此問題，請依照下列步驟執行︰

1. 移至 [ [Azure 入口網站](Https://portal.azure.com)]，選取您的 API 管理實例，然後選取 [**受控**識別]。 請確定 [**向 Azure Active Directory 註冊**] 選項已設為 **[是]** 。 
    向 Azure Active Directory](./media/api-management-troubleshoot-cannot-add-custom-domain/register-with-aad.png) 註冊 ![
1. 在 Azure 入口網站中，開啟 **金鑰保存庫** 服務，然後選取您要嘗試用於自訂網域的金鑰保存庫。
1. 選取 [**存取原則**]，然後檢查是否有符合 API 管理服務實例名稱的服務主體。 如果有，請選取服務主體，並確定它具有 [**秘密許可權**] 底下所列的 [**取得**] 許可權。  
    ![新增服務主體](./media/api-management-troubleshoot-cannot-add-custom-domain/access-policy.png) 的存取原則
1. 如果 [API 管理] 服務不在清單中，請選取 [**新增存取原則**]，然後建立下列存取原則：
    - **從範本設定**：無
    - **選取 [主體**]：搜尋 API 管理服務的名稱，然後從清單中加以選取
    - **金鑰許可權**：無
    - **秘密許可權**： Get
    - **憑證許可權**：無
1. 選取 **[確定]** 以建立存取原則。
1. 按一下 [儲存] 以儲存變更。

檢查問題是否已解決。 若要這麼做，請嘗試使用 Key Vault 憑證，在 API 管理服務中建立自訂網域。

## <a name="next-steps"></a>後續步驟
深入瞭解 API 管理服務：

- 查看更多有關 API 管理的 [視訊](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 。
* 如需其他保護後端服務的方式，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)。

* [建立 API 管理服務執行個體](get-started-create-service-instance.md)。
* [管理第一個 API](import-and-publish.md)。