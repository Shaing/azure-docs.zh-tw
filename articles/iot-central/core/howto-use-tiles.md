---
title: 如何在 Azure IoT Central 應用程式儀表板中使用磚 |Microsoft Docs
description: 作為建立者，瞭解如何在應用程式儀表板、裝置集合儀表板及裝置儀表板上使用磚。
author: v-krghan
ms.author: v-krghan
ms.date: 06/27/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0803258c362279049a49a7eee00e7a4763621672
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952624"
---
# <a name="how-to-use-tiles"></a>如何使用磚
您可以使用磚來自訂應用程式儀表板、裝置儀表板和裝置集合儀表板。 您可以將多個圖格新增至儀表板，並自訂這些磚以顯示與您的應用程式相關的資訊。 您也可以調整磚的大小，並自訂任何儀表板上的版面配置。 下列螢幕擷取畫面顯示具有不同磚的應用程式儀表板。

![應用程式儀表板](media/howto-use-tiles/image1a.png)


下表摘要說明 Azure IoT Central 中磚的使用方式：

 
| 圖格 | 儀表板 | 描述
| ----------- | ------- | ------- |
| 連結 | 應用程式和裝置集合儀表板 |連結磚是可按顯示標題和描述文字的磚。 使用 [連結] 磚可讓使用者流覽至與您的應用程式相關的 URL。 |
| 映像 | 應用程式和裝置集合儀表板 |影像磚會顯示自訂影像，而且可以按按一下。 使用影像磚將圖形新增至儀表板，並選擇性地讓使用者流覽至與您的應用程式相關的 URL。|
| 標籤 | 應用程式儀表板 |標籤磚：在儀表板上顯示自訂文字。 您可以選擇文字的大小。 使用標籤磚將相關資訊新增至儀表板，例如描述、連絡人詳細資料或協助|
| 對應 | 應用程式和裝置集合儀表板 |地圖底圖會在地圖上顯示裝置的位置和狀態。 例如，您可以顯示裝置的位置，以及風扇是否已開啟。|
| 折線圖 | 應用程式和裝置儀表板 |折線圖磚：顯示一段時間內裝置的匯總測量圖表。 例如，您可以顯示一個折線圖，其中顯示過去一小時內裝置的平均溫度和壓力|
| 長條圖 | 應用程式和裝置儀表板 |橫條圖磚：顯示裝置在一段時間內的匯總測量圖表。 例如，您可以顯示橫條圖，其中顯示過去一小時內裝置的平均溫度和壓力 |
| 事件歷程記錄 | 應用程式和裝置儀表板 |[事件歷程記錄] 磚會顯示裝置在一段時間內的事件。 例如，您可以使用它來顯示裝置在過去一小時內發生的所有溫度變更。 |
| 狀態記錄 | 應用程式和裝置儀表板 |[狀態歷程記錄] 圖格會顯示一段時間的度量值。 例如，您可以使用它來顯示過去一小時內裝置的溫度值。|
| KPI | 應用程式和裝置儀表板 | [KPI] 磚會顯示一段時間的匯總遙測或事件測量。 例如，您可以使用它來顯示過去一小時內裝置達到的最高溫度。|
| 上次已知值 | 應用程式和裝置儀表板 |[上次已知值] 磚會顯示遙測或狀態測量的最新值。 例如，您可以使用此磚顯示裝置的最新溫度、壓力和濕度測量。|
| 裝置集方格 | 應用程式和裝置集合儀表板 | [裝置設定] 方格會顯示裝置集的相關資訊。 使用 [裝置集方格] 磚來顯示裝置集合中所有裝置的名稱、位置和型號等資訊。|


若要深入瞭解如何在 Azure IoT Central 應用程式中設定儀表板，請參閱[將磚新增至儀表板](./howto-add-tiles-to-your-dashboard.md)。
