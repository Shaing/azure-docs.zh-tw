---
title: 如何運作 Azure MFA-Azure Active Directory
description: Azure Multi-Factor Authentication 不但能協助保護資料和應用程式免遭不當存取，還可符合使用者對簡易登入程序的需求。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0982f6fb70cd6866af48feab640d5dc36bcb6b28
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848675"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>運作方式：Azure Multi-Factor Authentication

雙步驟驗證的安全性仰賴其分層方法。 使用多重驗證因素會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探使用者的密碼，在不持有額外驗證方法的情況下便沒有任何意義。 其運作方式需要下列二個以上的驗證方法：

* 您知道的某些資訊 (通常是密碼)
* 您擁有的某些東西 (不容易輕易複製的信任裝置，例如電話)
* 您身上的某些特徵 (生物識別技術)

<center>

![的概念驗證方法影像](./media/concept-mfa-howitworks/methods.png)</center>

Azure Multi-Factor Authentication (MFA) 有助於保護對資料與應用程式的存取，同時讓使用者能夠方便使用。 它藉由要求第二種形式的驗證來提供額外的安全性，並透過一系列易於使用的[驗證方法](concept-authentication-methods.md)來提供增強式驗證。 因管理員所做的設定決定不同，使用者可能必須也可能無須通過 MFA。

## <a name="how-to-get-multi-factor-authentication"></a>如何取得 Multi-Factor Authentication？

Multi-Factor Authentication 隨附於下列供應項目：

* **Azure Active Directory Premium**或**Microsoft 365 商務版**-使用條件式存取原則進行 Azure 多因素驗證的完整功能使用，以要求多重要素驗證。

* **Azure AD Free**或獨立**Office 365**授權-使用預先建立的[條件式存取基準保護原則](../conditional-access/concept-baseline-protection.md)，為您的使用者和系統管理員要求多重要素驗證。

* **Azure Active Directory 全域管理員** - Azure Multi-Factor Authentication 功能子集可用來作為保護全域管理員帳戶的方法。

> [!NOTE]
> 自 2018 年 9 月 1 日起，新客戶無法再將 Azure Multi-Factor Authentication 當做獨立供應項目購買。 多重要素驗證將繼續為 Azure AD Premium 授權中的可用功能。

## <a name="supportability"></a>支援能力

因為大多數使用者都已習慣僅使用密碼來驗證，所以您的組織務必要與所有使用者溝通此程序。 這番了解可以避免使用者因為 MFA 的小問題就連絡技術人員。 不過，有一些案例是需要暫時停用 MFA。 使用下列指導方針了解如何處理這些案例：

* 訓練您的支援人員來處理使用者因無權存取其驗證方法或其無法正常運作而無法登入的案例。
   * 使用 Azure MFA 服務的條件式存取原則，您的支援人員可以將使用者新增至從要求 MFA 的原則中排除的群組。
* 請考慮使用名為「位置」的條件式存取，將雙步驟驗證提示減到最少。 有了這項功能，系統管理員可以針對從安全信任的網路位置（例如用於新使用者上線的網路區段）登入的使用者略過雙步驟驗證。
* 部署[Azure AD Identity Protection](../active-directory-identityprotection.md)並根據風險偵測來觸發雙步驟驗證。

## <a name="next-steps"></a>後續步驟

- [逐步執行 Azure 多因素驗證部署](howto-mfa-getstarted.md)
