---
title: 顯示地圖上座標的相關資訊 |Microsoft Azure 對應
description: 瞭解當使用者選取座標時，如何顯示地圖上位址的相關資訊。
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5450ada04a1af44a3fff0402b30540e899cc4dd5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911051"
---
# <a name="get-information-from-a-coordinate"></a>從座標取得資訊

本文會示範如何進行反向位址搜尋，顯示已按下的快顯位置本身的位址。

有兩種方式可以進行反向位址搜尋。 第一個方式是透過服務模組查詢 [Azure 地圖反向地址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)。 另一種方式是利用[FETCH API](https://fetch.spec.whatwg.org/)向[Azure 地圖服務的反向位址搜尋 api](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)提出要求，以尋找位址。 以下簡要說明這兩種方式。

## <a name="make-a-reverse-search-request-via-service-module"></a>透過服務模組提出反向搜尋要求

<iframe height='500' scrolling='no' title='從座標取得資訊 (服務模組)' src='//codepen.io/azuremaps/embed/ejEYMZ/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ejEYMZ/'>從座標取得資訊 (服務模組)</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建立地圖物件，並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會建立 `TokenCredential`，以使用存取權杖來驗證要 Azure 地圖服務的 HTTP 要求。 然後，它會將 `TokenCredential` 傳遞至 `atlas.service.MapsURL.newPipeline()`，並建立[管線](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest)實例。 `searchURL` 代表 Azure 地圖服務[搜尋](https://docs.microsoft.com/rest/api/maps/search)作業的 URL。

第三個程式碼區塊會將滑鼠游標的樣式更新為指標，並建立[popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第四個程式碼區塊會新增滑鼠 click[事件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)接聽程式。 觸發時，會使用所按下點的座標建立搜尋查詢。 接著，它會使用服務模組的[getSearchAddressReverse](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest#searchaddressreverse-aborter--geojson-position--searchaddressreverseoptions-)方法，針對座標的位址查詢[取得搜尋位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 。 然後會使用 `geojson.getFeatures()` 方法來解壓縮回應中的 GeoJSON 功能集合。

第五個程式碼區塊會設定 HTML 快顯視窗內容，以顯示已按下座標位置的回應位址。

游標變更、快顯物件和點選事件全都建立於地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以確保在可擷取座標資訊前完全載入地圖。

## <a name="make-a-reverse-search-request-via-fetch-api"></a>透過 Fetch API 提出反向搜尋要求

按一下對應，使用 fetch 對該位置進行反向地理編碼要求。

<iframe height='500' scrolling='no' title='從座標取得資訊' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>從座標取得資訊</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

在上述程式碼中，程式碼的第一個區塊會建立地圖物件，並將驗證機制設定為使用存取權杖。 如需相關指示，您可以查看[建立對應](./map-create.md)。

第二個程式碼區塊會將滑鼠游標的樣式更新為指標和[快顯](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)物件。 如需相關指示，您可以查看[在地圖上新增快顯](./map-add-popup.md)。

第三個程式碼區塊會新增滑鼠點選的事件接聽程式。 按下滑鼠時，它會利用[FETCH API](https://fetch.spec.whatwg.org/)來查詢[Azure 地圖服務的反向位址搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) ，以取得已按一下的座標位址。 對於成功的回應，它會收集所點選位置的地址，並透過快顯類別的 [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) 函式定義快顯內容和位置。

游標變更、快顯物件和點選事件全都建立於地圖的[載入事件接聽程式](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events)，以確保在可擷取座標資訊前完全載入地圖。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)

> [!div class="nextstepaction"]
> [顯示流量](./map-show-traffic.md)
