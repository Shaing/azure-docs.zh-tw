---
title: 教學課程：Azure Active Directory 與 Adaptive Insights 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Adaptive Insights 之間的單一登入。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232128"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>教學課程：整合 Adaptive Insights 與 Azure Active Directory

在本教學課程中，您會了解如何整合 Adaptive Insights 與 Azure Active Directory (Azure AD)。 在整合 Adaptive Insight 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Adaptive Insights 的人員。
* 讓使用者使用其 Azure AD 帳戶自動登入 Adaptive Insights。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必要條件

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 Adaptive Insights 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。

* Adaptive Insights 支援由 **IDP** 起始的 SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>從資源庫新增 Adaptive Insights

若要設定以將 Adaptive Insights 整合到 Azure AD 中，您需要從資源庫將 Adaptive Insights 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory]  服務。
1. 巡覽至 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]  區段的搜尋方塊中，輸入 **Adaptive Insights**。
1. 從結果面板選取 [Adaptive Insights]  ，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>設定和測試 Azure AD 單一登入

以名為 **B.Simon** 的測試使用者，設定及測試與 Adaptive Insights 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Adaptive Insights 中相關使用者之間的連結關聯性。

若要設定及測試與 Adaptive Insights 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** - 讓您的使用者能夠使用此功能。
2. **[設定 Adaptive Insights SSO](#configure-adaptive-insights-sso)** - 在應用程式端設定單一登入設定。
3. **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** - 使用 B.Simon 測試 Azure AD 單一登入。
4. **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** - 讓 B.Simon 能夠使用 Azure AD 單一登入。
5. **[建立 Adaptive Insights 測試使用者](#create-adaptive-insights-test-user)** - 使 Adaptive Insights 中對應的 B.Simon 連結到該使用者在 Azure AD 中的代表項目。
6. **[測試 SSO](#test-sso)** - 驗證組態是否能運作。

### <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Adaptive Insights]  應用程式整合頁面上，尋找 [管理]  區段並選取 [單一登入]  。
1. 在 [選取單一登入方法]  頁面上，選取 [SAML]  。
1. 在 [以 SAML 設定單一登入]  頁面上，按一下 [基本 SAML 設定]  的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 組態]  區段上，執行下列步驟：

    a. 在 [識別碼]  文字方塊中，使用下列模式來輸入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. 在 [回覆 URL]  文字方塊中，使用下列模式來輸入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > 您可以從 Adaptive Insights 的 [SAML SSO 設定]  頁面取得 [識別碼 (實體識別碼)] 和 [回覆 URL] 值。

4. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

    ![憑證下載連結](common/certificatebase64.png)

6. 在 [設定 Adaptive Insights]  區段上，依據您的需求複製適當的 URL。

    ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>設定 Adaptive Insights SSO

1. 在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Adaptive Insights 公司網站。

2. 移至 [管理]  。

    ![管理員](./media/adaptivesuite-tutorial/ic805644.png "Admin")

3. 在 [使用者和角色]  區段中，按一下 [SAML SSO 設定]  。

    ![管理 SAML SSO 設定](./media/adaptivesuite-tutorial/ic805645.png "管理 SAML SSO 設定")

4. 在 [SAML SSO 設定]  頁面上，執行下列步驟：

    ![SAML SSO 設定](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO 設定")

    a. 在 [識別提供者名稱]  文字方塊中，輸入您組態的名稱。

    b. 將從 Azure 入口網站複製的 [Azure AD 識別碼]  值貼到 [識別提供者實體識別碼]  文字方塊中。

    c. 將從 Azure 入口網站複製的 [登入 URL]  值貼到 [識別提供者 SSO URL]  文字方塊中。

    d. 將從 Azure 入口網站複製的 [登出 URL]  值貼到 [自訂登出 URL]  文字方塊中。

    e. 若要上傳您下載的憑證，請按一下 [選擇檔案]  。

    f. 選取下列選項：

     * 針對 [SAML 使用者識別碼]  ，選取 [使用者的 Adaptive Insights 使用者名稱]  。

     * 針對 [SAML 使用者識別碼位置]  ，選取 [主旨 NameID 中的使用者識別碼]  。

     * 針對 [SAML NameID 格式]  ，選取 [電子郵件地址]  。

     * 針對 [啟用 SAML]  ，選取 [允許 SAML SSO 和直接 Adaptive Insights 登入]  。

    g. 複製 [Adaptive Insights SSO URL]  ，並貼到 Azure 入口網站中 [基本 SAML 組態]  區段的 [識別碼 (實體識別碼)]  和 [回覆 URL]  文字方塊中。

    h. 按一下 [檔案]  。

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您將在 Azure 入口網站中建立名為 B.Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]  。
1. 在 [使用者]  屬性中，執行下列步驟：
   1. 在 [名稱]  欄位中，輸入 `B.Simon`。  
   1. 在 [使用者名稱]  欄位中，輸入 username@companydomain.extension。 例如： `B.Simon@contoso.com` 。
   1. 選取 [顯示密碼]  核取方塊，然後記下 [密碼]  方塊中顯示的值。
   1. 按一下頁面底部的 [新增]  。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Adaptive Insights 的存取權授與 B.Simon，讓該使用者能使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Adaptive Insights]  。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]  ，然後在 [新增指派]  對話方塊中選取 [使用者和群組]  。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組]  對話方塊的 [使用者] 清單中選取 [B.Simon]  ，然後按一下畫面底部的 [選取]  按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色]  對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取]  按鈕。
1. 在 [新增指派]  對話方塊中，按一下 [指派]  按鈕。

### <a name="create-adaptive-insights-test-user"></a>建立 Adaptive Insights 測試使用者

若要讓 Azure AD 使用者能夠登入 Adaptive Insights，必須將他們佈建到 Adaptive Insights。 Adaptive Insights 需以手動方式佈建。

**若要設定使用者佈建，請執行下列步驟：**

1. 以系統管理員身分登入您的 **Adaptive Insights** 公司網站。

2. 移至 [管理]  。

   ![管理員](./media/adaptivesuite-tutorial/IC805644.png "Admin")

3. 在 [使用者和角色]  區段中，按一下 [使用者]  。

   ![加入使用者](./media/adaptivesuite-tutorial/IC805648.png "新增使用者")

4. 在 [新增使用者]  區段中，執行下列步驟：

   ![提交](./media/adaptivesuite-tutorial/IC805649.png "提交")

   a. 在相關的文字方塊中，輸入您想要佈建之有效 Azure Active Directory 使用者的 [名稱]  、[使用者名稱]  、[電子郵件]  、[密碼]  。

   b. 選取 [角色]  。

   c. 按一下 [提交]  。

> [!NOTE]
> 您可以使用任何其他 Adaptive Insights 使用者帳戶建立工具，或是由 Adaptive Insights 提供的 API，來佈建 Azure AD 使用者帳戶。

### <a name="test-sso"></a>測試 SSO 

在本節中，您會使用存取面板來測試您的 Azure AD 單一登入設定。

當您在存取面板中按一下 [Adaptive Insights] 圖格時，應該會自動登入您已設定 SSO 的 Adaptive Insights。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

