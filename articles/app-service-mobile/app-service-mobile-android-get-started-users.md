---
title: 在 Android 上新增驗證
description: 瞭解如何使用 Azure App Service，透過 Google、Facebook、Twitter 和 Microsoft 等身分識別提供者驗證 Android 應用程式的使用者。
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f68b4f8477d5b21a7107270370af387a7e88756e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668940"
---
# <a name="add-authentication-to-your-android-app"></a>將驗證加入 Android 應用程式中
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center 支援使用端對端及整合服務中心來開發行動應用程式。 開發人員可以使用**建置**、**測試**和**散發**服務來設定持續整合及傳遞管線。 部署應用程式之後，開發人員可以使用**分析**和**診斷**服務來監視其應用程式的狀態和使用情況，並使用**推送**服務與使用者互動。 開發人員也可以利用**驗證**來驗證其使用者，並使用**資料**來保存及同步雲端中的應用程式資料。
>
> 如果您想要在行動應用程式中整合雲端服務，請立即註冊 [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) \(英文\)。

## <a name="summary"></a>總結
在本教學課程中，您可以使用支援的身分識別提供者，將驗證加入 Android 上的 TodoList 快速入門專案。 本教學課程以[開始使用 Mobile Apps] 為基礎，您必須先完成該教學課程。

## <a name="register"></a>註冊應用程式進行驗證，並設定 Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>將您的應用程式新增至允許的外部重新導向 URL

安全的驗證會要求您為應用程式定義新的 URL 配置。 這讓驗證系統能夠在驗證程序完成之後，重新導向回到您的應用程式。 我們會在這整個教學課程中使用 URL 配置 appname。 不過，您可以使用任何您選擇的 URL 結構描述。 它對於您的行動應用程式而言應該是唯一的。 在伺服器端啟用重新導向：

1. 在 [Azure 入口網站]中，選取您的 App Service。

2. 按一下 [驗證/授權] 功能表選項。

3. 在 [允許的外部重新導向 URL] 中，輸入 `appname://easyauth.callback`。  此字串中的 appname 是您行動應用程式的 URL 配置。  它必須遵循通訊協定的標準 URL 規格 (只使用字母和數字，並以字母為開頭)。  請記下您選擇的字串，因為您將需要在數個位置中使用該 URL 配置來調整您的行動應用程式程式碼。

4. 按一下 [確定]。

5. 按一下 [儲存]。

## <a name="permissions"></a>限制只有通過驗證的使用者具有權限
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* 在 Android Studio 中，開啟您使用[開始使用 Mobile Apps] 教學課程完成的專案。 從 [執行] 功能表按一下 [執行應用程式] 並確認應用程式啟動之後會引發無法處理的例外狀況，狀態碼為 401 (未授權)。

     發生此例外狀況是因為應用程式嘗試以未驗證的使用者身分來存取後端，但 *TodoItem* 資料表現在需要驗證。

接下來，您要將應用程式更新為在要求 Mobile Apps 後端的資源之前必須驗證使用者。

## <a name="add-authentication-to-the-app"></a>將驗證新增至應用程式
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>在用戶端快取驗證權杖
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>後續步驟
現在您已經完成了這個基本驗證的教學課程，可以考慮繼續進行下列其中一個教學課程：

* [將推播通知新增至 Android 應用程式](app-service-mobile-android-get-started-push.md)。
  了解如何設定 Mobile Apps 後端，使用 Azure 通知中樞來傳送推播通知。
* [啟用 Android 應用程式的離線同步處理](app-service-mobile-android-get-started-offline-data.md)。
  了解如何使用 Mobile Apps 後端，將離線支援新增至應用程式。 使用離線同步處理時，使用者可與行動應用程式進行互動&mdash;檢視、新增或修改資料&mdash;即使沒有網路連線進也可行。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[開始使用 Mobile Apps]: app-service-mobile-android-get-started.md
[Azure 入口網站]: https://portal.azure.com/
