---
title: 什麼是 Azure AD 聯結的裝置？
description: 了解裝置身分識別管理如何協助您管理存取您環境中資源的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24ec4373bceb3cc3c9e5be2c7a0dab1f62197b3c
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76512193"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

Azure AD join 適用于想要成為雲端優先或僅限雲端的組織。 任何組織都可以部署 Azure AD 加入的裝置，不論大小或產業為何。 即使在混合式環境中，Azure AD join 仍可運作，同時允許雲端和內部部署應用程式和資源的存取。

|   | Azure AD Join |
| --- | --- |
| **[定義]** | 僅聯結至需要組織帳戶才能登入裝置 Azure AD |
| **主要物件** | 適用于僅限雲端和混合式組織。 |
|   | 適用于組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | 所有 Windows 10 裝置 |
| **佈建** | 自助： Windows OOBE 或設定 |
|   | 大量註冊 |
|   | Windows Autopilot |
| **裝置登入選項** | 組織帳戶使用： |
|   | 密碼 |
|   | Windows Hello 企業版 |
|   | FIDO 2.0 安全性金鑰（預覽） |
| **裝置管理** | 行動裝置管理（範例： Microsoft Intune） |
|   | Microsoft Intune 與 Microsoft 端點的共同管理 Configuration Manager |
| **主要功能** | SSO 到雲端和內部部署資源 |
|   | 透過 MDM 註冊和 MDM 合規性評估的條件式存取 |
|   | 在鎖定畫面上進行自助式密碼重設和 Windows Hello PIN 重設 |
|   | 跨裝置企業狀態漫遊 |

已加入 Azure AD 的裝置會使用組織 Azure AD 帳戶登入。 根據套用至裝置身分識別的 Azure AD 帳戶和[條件式存取原則](../conditional-access/overview.md)，可以進一步限制組織中資源的存取權。

系統管理員可以使用行動裝置管理（MDM）工具（例如 Microsoft Intune 或使用 Microsoft 端點 Configuration Manager 的共同管理案例）來保護及進一步控制已加入 Azure AD 的裝置。 這些工具可讓您強制執行組織所需的設定，例如要求儲存加密、密碼複雜性、軟體安裝和軟體更新。 系統管理員可以使用 Configuration Manager，將組織應用程式提供給 Azure AD 加入的裝置，以[管理商務和教育 Microsoft Store 的應用](https://docs.microsoft.com/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)程式。

使用全新體驗（OOBE）、大量註冊或[Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot)等自助選項，即可完成 Azure AD 聯結。

Azure AD 已加入的裝置仍可在組織的網路上，維持對內部部署資源的單一登入存取權。 Azure AD 聯結的裝置仍然可以驗證內部部署伺服器，例如檔案、列印和其他應用程式。

## <a name="scenarios"></a>案例

雖然 Azure AD Join 主要適用於沒有內部部署 Windows Server Active Directory 基礎結構的組織，但您也可以在下列情況下使用它：

- 您想要使用 Azure AD 和 MDM (例如 Intune) 來轉換成雲端式基礎結構。
- 例如，如果您需要取得行動裝置 (例如控制下的平板電腦和電話)，您無法使用內部部署網域加入。
- 您的使用者主要需要存取 Office 365 或與 Azure AD 整合的其他 SaaS 應用程序。
- 您想要管理 Azure AD 中的使用者群組，而不是 Active Directory 中的使用者群組。 例如，您可以將此案例套用至季節性工作者、承包商或學生。
- 您要為內部部署基礎結構受到限制的遠端分公司工作者提供加入功能。

您可以設定適用於 Windows 10 裝置的 Azure AD 已加入裝置。

Azure AD 加入裝置的目標是簡化：

- Windows 部署工作用的裝置
- 從任何 Windows 裝置存取組織應用程式與資源
- 工作用裝置的雲端式管理
- 使用者使用其 Azure AD 登入其裝置，或 Active Directory 工作或學校帳戶同步處理。

![Azure AD 加入裝置](./media/concept-azure-ad-join/azure-ad-joined-device.png)

您可以使用下列任何一種方法來部署 Azure AD Join：

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [大量部署](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [自助體驗](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>後續步驟

- [規劃 Azure AD Join 實作](azureadjoin-plan.md)
- [如何管理 Azure AD 加入的裝置上的本機系統管理員群組](assign-local-admin.md)
- [使用 Azure 入口網站管理裝置身分識別](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
