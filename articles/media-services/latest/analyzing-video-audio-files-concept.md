---
title: 分析影片和音訊檔案
titleSuffix: Azure Media Services
description: 瞭解如何在 Azure 媒體服務中使用 AudioAnalyzerPreset 和 VideoAnalyzerPreset 分析音訊和影片內容。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/21/2019
ms.author: juliako
ms.openlocfilehash: 23d546d6adcdb91b4ef4702b81fe77536fe9f3d3
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186267"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>使用 Azure 媒體服務分析影片和音訊檔案

Azure 媒體服務 v3 可讓您使用影片索引子，從您的影片和音訊檔案中摘錄見解。 本文說明用來解壓縮這些深入解析的媒體服務 v3 分析器預設值。 若您希望獲得更多詳細深入解析，請直接使用影片索引器。 若要瞭解使用影片索引子與媒體服務分析器預設值的時機，請參閱[比較檔](../video-indexer/compare-video-indexer-with-media-services-presets.md)。

若要使用媒體服務 v3 預設值來分析您的內容，請建立**轉換**，並提交使用下列其中一個預設值的**作業**： [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)或**AudioAnalyzerPreset**。 如需示範如何使用**VideoAnalyzerPreset**的教學課程，請參閱[使用 Azure 媒體服務分析](analyze-videos-tutorial-with-api.md)影片。

> [!NOTE]
> 使用視訊或音訊分析程式的預設時，可使用 Azure 入口網站將帳戶設定為擁有 10 個 S3 編碼保留單元。 如需詳細資訊，請參閱[調整媒體處理](media-reserved-units-cli-how-to.md)。

## <a name="built-in-presets"></a>內建預設

媒體服務目前支援下列內建的分析器預設：  

|**預設名稱**|**案例**|**詳細資料**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|分析音訊|此預設會套用一組預先定義的 AI 型分析作業，包括語音轉譯。 此預設目前支援處理具有單一音訊播放軌 (包含單一語言的語音) 的內容。 您可以使用「語言標記-區域」的 BCP-47 格式，為輸入中的音訊承載指定語言。 支援的語言為英文（' en-us ' 與 ' en-us '）、西班牙文（' es ' 和 ' es '）、法文（' fr-fr '）、義大利文（' it '）、日文（' ja-jp '）、葡萄牙文（' pt-BR '）、中文（' zh-CN '）、德文（' de '）、阿拉伯文（' ar-例如 ' 和 ' ar-SY '）、俄文（' ru-RU '）、印度文（「hi」）和韓文（' ko-KR '）。<br/><br/> 如果語言未指定或設為 null，自動語言偵測會選擇偵測到的第一個語言，並在檔案持續期間繼續使用選取的語言。 自動語言偵測功能目前支援英文、簡體中文、法文、德文、義大利文、日文、西班牙文、俄文和葡萄牙文。 它不支援在偵測到第一個語言之後，以動態方式切換語言。 搭配語音清晰的錄音時，自動語言偵測功能的效果最好。 如果自動語言偵測找不到語言，則轉譯會回復成英文。|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|分析音訊和視訊|從音訊和視訊擷取見解 (豐富的中繼資料)，並輸出 JSON 格式檔案。 您可以指定在處理視訊檔案時，是否只想擷取音訊見解。 如需詳細資訊，請參閱[分析視訊](analyze-videos-tutorial-with-api.md)。|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|偵測出現在影片中的人臉|說明分析影片以偵測所有表面時所要使用的設定。|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

預設可讓您從音訊檔案或視訊檔案擷取多個音訊深入資訊。 輸出會包含 JSON 檔案 (包含所有的深入資訊) 和音訊文字記錄的 VTT 檔案。 此預設接受的屬性會指定輸入檔案的語言 ([BCP47](https://tools.ietf.org/html/bcp47) 字串形式)。 音訊的深入資訊包括：

* **音訊**轉譯：包含時間戳記的單字文字記錄。 支援多種語言。
* **說話者編制索引**：喇叭與對應說話單字的對應。
* **語音情感分析**：在音訊轉譯上執行之情感分析的輸出。
* **關鍵字**：從音訊轉譯中解壓縮的關鍵字。

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

預設可讓您從影片檔案擷取多個音訊和影片深入資訊。 輸出包含 JSON 檔案 (包含所有的深入資訊)、視訊文字記錄的 VTT 檔案，以及縮圖的集合。 此預設也接受 [BCP47](https://tools.ietf.org/html/bcp47) 字串 (表示視訊的語言) 當作屬性。 視訊深入資訊包含上述的音訊深入資訊和下列其他項目：

* **臉部追蹤**：影片中出現臉部的時間。 每個臉部都有一個臉部識別碼和一個對應的縮圖集合。
* **視覺化文字**：透過光學字元辨識偵測到的文字。 文字會加上時間戳記，也用來解壓縮關鍵字（除了音訊文字記錄之外）。
* 主要畫面**格：從**影片解壓縮的主要畫面格集合。
* **視覺內容仲裁**：本質上標示為成人或猥褻的影片部分。
* **批註**：根據預先定義的物件模型來標注影片的結果

## <a name="insightsjson-elements"></a>insights.json 元素

輸出會包含 JSON 檔案（深入解析），其中包含在影片或音訊中找到的所有見解。 JSON 可能包含下列元素：

### <a name="transcript"></a>文字記錄

|名稱|描述|
|---|---|
|id|行識別碼。|
|文字|文字記錄本身。|
|語言|文字記錄語言。 用於支援文字記錄，其中每一行可以有不同的語言。|
|執行個體|這一行曾出現的時間範圍清單。 如果執行個體是文字記錄，它只能有 1 個執行個體。|

範例：

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|名稱|描述|
|---|---|
|id|OCR 行識別碼。|
|文字|OCR 文字。|
|信賴度|辨識信賴。|
|語言|OCR 語言。|
|執行個體|此 OCR 曾出現的時間範圍清單 (相同的 OCR 可以出現多次)。|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>臉部

|名稱|描述|
|---|---|
|id|臉部識別碼。|
|名稱|臉部名稱。 它可以是「未知的 #0」、已識別的名人或客戶訓練的人員。|
|信賴度|臉部識別信賴。|
|Description|名人的描述。 |
|thumbnailId|該臉部的縮圖識別碼。|
|knownPersonId|內部識別碼（如果是已知的人員）。|
|referenceId|Bing 識別碼（如果是 Bing 名人）。|
|referenceType|目前只是 Bing。|
|title|標題（如果是名人）（例如「Microsoft 的 CEO」）。|
|imageUrl|影像 URL （如果是名人）。|
|執行個體|臉部出現在指定時間範圍內的實例。 每個執行個體也會有 thumbnailsId。 |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>擷取畫面

|名稱|描述|
|---|---|
|id|擷取畫面識別碼。|
|keyFrames|擷取畫面的主要畫面清單 (每個主要畫面都有一個識別碼和執行個體的時間範圍清單)。 主要畫面格執行個體中有縮圖識別碼欄位，其中包含主要畫面格的縮圖識別碼。|
|執行個體|此擷取畫面的時間範圍清單 (擷取畫面只能有 1 個執行個體)。|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>統計資料

|名稱|描述|
|---|---|
|CorrespondenceCount|影片中的對應數目。|
|WordCount|每個說話者的字數。|
|SpeakerNumberOfFragments|說話者在影片中的片段數量。|
|SpeakerLongestMonolog|說話者最長的獨白。 如果說話者在 monolog 內有讓閉嘴，它就會包含在內。 獨白開頭和結尾的無聲部分則會被移除。|
|SpeakerTalkToListenRatio|將說話者獨白的時間 (不含無聲的部分) 除以影片的總時間長度。 時間會四捨五入至小數點第三位。|


### <a name="sentiments"></a>人氣

人氣會依據其 sentimentType 欄位 (Positive/Neutral/Negative) 加以彙總。 例如：0-0.1、0.1-0.2。

|名稱|描述|
|---|---|
|id|人氣識別碼。|
|averageScore |所有該人氣類型執行個體的總分平均值 - Positive/Neutral/Negative|
|執行個體|此人氣曾出現的時間範圍清單。|
|sentimentType |類型可以是「正面」、「中性」或「負面」。|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>標籤

|名稱|描述|
|---|---|
|id|標籤識別碼。|
|名稱|標籤名稱 (例如，電腦、電視)。|
|語言|標籤名稱語言 (轉譯時)。 BCP-47|
|執行個體|此標籤曾出現的時間範圍清單 (同一個標籤可以出現多次)。 每個執行個體都有一個信賴度欄位。 |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>關鍵字

|名稱|描述|
|---|---|
|id|關鍵字識別碼。|
|文字|關鍵字。|
|信賴度|關鍵字的辨識信賴。|
|語言|關鍵字語言 (轉譯時)。|
|執行個體|此關鍵字曾出現的時間範圍清單 (同一個關鍵字可以出現多次)。|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 區塊包含影片索引器偵測到可能含有成人內容的時間範圍。 如果 visualContentModeration 是空的，則不會識別任何成人內容。

經發現含有成人或猥褻內容的影片，只能供私人檢視。 使用者可以提交內容的人工審核要求，在此情況下，`IsAdult` 屬性會包含人工審核的結果。

|名稱|描述|
|---|---|
|id|視覺內容仲裁識別碼。|
|adultScore|成人分數 (由內容仲裁提供)。|
|racyScore|辛辣分數 (由內容仲裁提供)。|
|執行個體|視覺內容仲裁出現的時間範圍清單。|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>後續步驟

[教學課程：使用 Azure 媒體服務分析視訊](analyze-videos-tutorial-with-api.md)
