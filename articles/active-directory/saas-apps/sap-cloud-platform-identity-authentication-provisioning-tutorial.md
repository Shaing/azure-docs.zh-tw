---
title: 教學課程：使用 Azure Active Directory 設定 SAP Cloud Platform Identity Authentication 以進行自動使用者布建 |Microsoft Docs
description: 瞭解如何設定 Azure Active Directory，以將使用者帳戶自動布建和取消布建至 SAP Cloud Platform Identity Authentication。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f49b5738-c769-403b-8f29-84ddeea7fbf1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: 266e68b2378db7148649fd4067f1da6172932367
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833843"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>教學課程：設定 SAP Cloud Platform Identity Authentication 以進行自動使用者布建

本教學課程的目的是要示範在 SAP Cloud Platform Identity Authentication 和 Azure Active Directory （Azure AD）中執行的步驟，以設定 Azure AD 自動布建和取消布建使用者和/或群組至 SAP Cloud Platform Identity Authentication。

> [!NOTE]
> 本教學課程會說明建置在 Azure AD 使用者佈建服務之上的連接器。 如需此服務的用途、運作方式和常見問題等重要詳細資訊，請參閱[使用 Azure Active Directory 對 SaaS 應用程式自動佈建和取消佈建使用者](../manage-apps/user-provisioning.md)。
>
> 此連接器目前為公開預覽版。 如需預覽功能之一般 Microsoft Azure 使用規定的詳細資訊，請參閱[Microsoft Azure 預覽的補充使用](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)規定。

## <a name="prerequisites"></a>必要條件

本教學課程中概述的案例假設您已經具有下列必要條件：

* Azure AD 租用戶
* [SAP Cloud Platform Identity Authentication 租使用者](https://cloudplatform.sap.com/pricing.html)
* SAP Cloud Platform Identity Authentication 中具有系統管理員許可權的使用者帳戶。

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>將使用者指派給 SAP Cloud Platform Identity Authentication

Azure Active Directory 使用稱為「*指派*」的概念，來判斷哪些使用者應接收所選應用程式的存取權。 在自動使用者布建的內容中，只有已指派給 Azure AD 中應用程式的使用者和/或群組會進行同步處理。

在設定並啟用自動使用者布建之前，您應該決定 Azure AD 中的哪些使用者和/或群組需要存取 SAP Cloud Platform Identity Authentication。 一旦決定後，您可以遵循此處的指示，將這些使用者和/或群組指派給 SAP Cloud Platform Identity Authentication：
* [將使用者或群組指派給企業應用程式](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>將使用者指派給 SAP Cloud Platform Identity Authentication 的重要秘訣

* 建議將單一 Azure AD 使用者指派給 SAP Cloud Platform Identity Authentication，以測試自動使用者布建設定。 其他使用者及/或群組可能會稍後再指派。

* 將使用者指派給 SAP Cloud Platform Identity Authentication 時，您必須在 [指派] 對話方塊中選取任何有效的應用程式特定角色（如果有的話）。 具有**預設存取**角色的使用者會從佈建中排除。

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>設定 SAP Cloud Platform Identity Authentication 以進行布建

1. 登入您的[SAP Cloud Platform Identity Authentication 管理主控台](https://sapmsftintegration.accounts.ondemand.com/admin)。 流覽至 **& 授權 > 系統管理員的使用者**。

    ![SAP Cloud Platform Identity Authentication 管理主控台](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  建立系統管理使用者，並選取使用者。  

3.  在 [設定授權] 底下，針對 [**管理使用者**] 和 [**管理群組**] 開啟切換按鈕。

    ![SAP 雲端平臺身分識別驗證新增 SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. 您將會收到一封電子郵件，可啟用您的帳戶並設定**SAP Cloud Platform Identity Authentication 服務**的密碼。

4.  複製 [**使用者識別碼**] 和 [**密碼**]。 這些值會分別在 [管理員使用者名稱] 和 [管理員密碼] 欄位中輸入 Azure 入口網站中 SAP Cloud Platform Identity Authentication 應用程式的 [布建] 索引標籤。

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>從資源庫新增 SAP Cloud Platform Identity Authentication

設定 SAP Cloud Platform Identity Authentication 以 Azure AD 自動布建使用者之前，您需要從 Azure AD 應用程式庫將 SAP Cloud Platform Identity Authentication 新增至受控 SaaS 應用程式清單。

**若要從 Azure AD 應用程式庫新增 SAP Cloud Platform Identity Authentication，請執行下列步驟：**

1. 在 **[Azure 入口網站](https://portal.azure.com)** 的左側導覽窗格中，選取 [ **Azure Active Directory**]。

    ![Azure Active Directory 按鈕](common/select-azuread.png)

2. 移至 [企業應用程式]，然後選取 [所有應用程式]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

3. 若要新增新的應用程式，請選取窗格頂端的 [**新增應用程式**] 按鈕。

    ![新增應用程式按鈕](common/add-new-app.png)

4. 在搜尋方塊中，輸入**Sap Cloud Platform Identity authentication**，在結果面板中選取 [ **Sap Cloud platform identity authentication** ]，然後按一下 [**新增**] 按鈕以新增應用程式。

    ![結果清單中的 SAP Cloud Platform Identity Authentication](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>設定 SAP Cloud Platform Identity Authentication 的自動使用者布建 

本節將引導您逐步設定 Azure AD 布建服務，以根據 Azure AD 中的使用者和/或群組指派，在 SAP Cloud Platform Identity Authentication 中建立、更新和停用使用者和/或群組。

> [!TIP]
> 您也可以選擇啟用 SAP Cloud Platform Identity Authentication 的 SAML 型單一登入，請遵循[Sap Cloud Platform Identity Authentication 單一登入教學](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)課程中提供的指示。 單一登入可以與自動使用者布建分開設定，雖然這兩個功能彼此的補充

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>若要在 Azure AD 中設定 SAP Cloud Platform Identity Authentication 的自動使用者布建：

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [**企業應用程式**]，然後選取 [**所有應用程式**]。

    ![企業應用程式刀鋒視窗](common/enterprise-applications.png)

2. 在應用程式清單中，選取 **SAP Cloud Platform Identity Authentication**。

    ![應用程式清單中的 SAP Cloud Platform Identity Authentication 連結](common/all-applications.png)

3. 選取 [佈建] 索引標籤。

    ![布建索引標籤](common/provisioning.png)

4. 將 [佈建模式] 設定為 [自動]。

    ![布建索引標籤](common/provisioning-automatic.png)

5. 在 [**管理員認證**] 區段下，于 [**租使用者 URL**] 中輸入 `https://<tenantID>.accounts.ondemand.com/service/scim `。 分別在 [**管理員使用者名稱**] 和 [**管理員密碼**] 中，輸入先前抓取的**使用者識別碼**和**密碼**值。 按一下 [**測試**連線] 以確保 Azure AD 可以連線至 SAP Cloud Platform Identity Authentication。 如果連線失敗，請確定您的 SAP 雲端平臺身分識別驗證帳戶具有系統管理員許可權，然後再試一次。

    ![租使用者 URL + 權杖](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. 在 [通知電子郵件] 欄位中，輸入應該收到佈建錯誤通知的個人或群組電子郵件地址，然後選取 [發生失敗時傳送電子郵件通知] 核取方塊。

    ![通知電子郵件](common/provisioning-notification-email.png)

7. 按一下 [儲存]。

8. **在 [對應**] 區段下，選取 [**同步處理 AZURE ACTIVE DIRECTORY 使用者至 SAP 雲端平臺身分識別驗證**]。

    ![SAP 雲端平臺身分識別驗證使用者對應](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. 在 [**屬性對應**] 區段中，檢查從 Azure AD 同步至 SAP Cloud Platform Identity Authentication 的使用者屬性。 選取為 [比對] 屬性**的屬性會**用來比對 SAP Cloud Platform Identity Authentication 中的使用者帳戶，以進行更新作業。 選取 [儲存] 按鈕以認可所有變更。

    ![SAP 雲端平臺身分識別驗證使用者屬性](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. 若要設定範圍篩選，請參閱[範圍篩選教學課程](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)中提供的下列指示。

11. 若要啟用 SAP Cloud Platform Identity Authentication 的 Azure AD 布建服務，請在 [**設定**] 區段中，將 [布建**狀態**] 變更為 [**開啟**]。

    ![布建狀態已切換為開啟](common/provisioning-toggle-on.png)

12. 在 [**設定**] 區段的 [**範圍**] 中選擇所需的值，以定義您想要布建至 SAP Cloud Platform Identity Authentication 的使用者和/或群組。

    ![布建範圍](common/provisioning-scope.png)

13. 當您準備好要佈建時，按一下 [儲存]。

    ![正在儲存布建設定](common/provisioning-configuration-save.png)

此作業會對在 [設定] 區段的 [範圍] 中定義的所有使用者和/或群組，啟動首次同步處理。 初始同步處理會比後續同步處理花費更多時間執行，只要 Azure AD 佈建服務正在執行，這大約每 40 分鐘便會發生一次。 您可以使用 [**同步處理詳細資料**] 區段來監視進度，並遵循連結來布建活動報告，其中描述 SAP Cloud Platform Identity Authentication 上的 Azure AD 布建服務所執行的所有動作。

如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](../manage-apps/check-status-user-account-provisioning.md)。

## <a name="connector-limitations"></a>連接器限制

* SAP Cloud Platform Identity Authentication 的 SCIM 端點需要特定格式的某些屬性。 您可以在[這裡](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#)深入瞭解這些屬性及其特定格式。

## <a name="additional-resources"></a>其他資源

* [管理企業應用程式的使用者帳戶佈建](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](../manage-apps/check-status-user-account-provisioning.md)

