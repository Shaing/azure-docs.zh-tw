---
title: 從 iOS 連接到 Windows 虛擬桌面-Azure
description: 如何使用 iOS 用戶端連接到 Windows 虛擬桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: helohr
ms.openlocfilehash: 472b8800a09cb12bc3e703ef53d0aab2b578e0d3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460230"
---
# <a name="connect-with-the-ios-client"></a>與 iOS 用戶端連線

> 適用于： iOS 13.0 或更新版本。 與 iPhone、iPad 和 iPod touch 相容。

您可以使用可下載的用戶端，從您的 iOS 裝置存取 Windows 虛擬桌面資源。 本指南將告訴您如何設定 iOS 用戶端。

## <a name="install-the-ios-client"></a>安裝 iOS 用戶端

若要開始使用，請在您的 iOS 裝置上[下載](https://aka.ms/rdios)並安裝用戶端。

## <a name="subscribe-to-a-feed"></a>訂閱摘要

訂閱您的系統管理員所提供的摘要，以取得您可以在 iOS 裝置上存取的受控資源清單。

若要訂閱摘要：

1. 在 [連接中心] 中，依序按一下 [ **+** ] 和 [**新增工作區**]。
2. 在 [摘要**url** ] 欄位中輸入摘要 url。 摘要 URL 可以是 URL 或電子郵件地址。
   - 如果您使用 URL，請使用您的系統管理員提供給您的帳戶。 通常會 <https://rdweb.wvd.microsoft.com>URL。
   - 若要使用電子郵件，請輸入您的電子郵件地址。 如果您的系統管理員以這種方式設定伺服器，這會告訴用戶端搜尋與您的電子郵件地址相關聯的 URL。
3. 點選 [下一步]。
4. 出現提示時，請提供您的認證。
   - 針對 [**使用者名稱**]，為使用者名稱提供存取資源的許可權。
   - 針對 [**密碼**]，提供與使用者名稱相關聯的密碼。
   - 如果您的系統管理員以這種方式設定驗證，可能也會提示您提供其他因素。
5. 點選 [儲存]。

在此之後，連接中心應該會顯示遠端資源。

訂閱摘要之後，摘要的內容將會定期自動更新。 根據系統管理員所做的變更，可能會新增、變更或移除資源。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何使用 iOS Beta 版用戶端，請參閱[開始使用 ios 用戶端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios)檔。
