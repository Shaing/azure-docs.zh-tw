---
title: Keyphrase 預先建立的實體-LUIS
titleSuffix: Azure Cognitive Services
description: 本文包含 Language Understanding (LUIS) 中預先建置的 keyPhrase 實體資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 51d1bd515651824545d486207ad4a74476aa7092
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491275"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>LUIS 應用程式的 KeyPhrase 預先建置實體
KeyPhrase 實體會從語句中解壓縮各種主要片語。 您不需要將包含 keyPhrase 的範例語句新增至應用程式。 [許多文化](luis-language-support.md#languages-supported)特性都支援 keyPhrase 實體，做為[文字分析](../text-analytics/overview.md)功能的一部分。 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>解析預先建置的 keyPhrase 實體

系統會針對查詢傳回下列實體物件：

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-responsetabv3"></a>[V3 回應](#tab/V3)

下列 JSON 會將 `verbose` 參數設定為 `false`：

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 詳細回應](#tab/V3-verbose)
下列 JSON 會將 `verbose` 參數設定為 `true`：

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 回應](#tab/V2)

下列範例示範如何解析 **builtin.keyPhrase** 實體。

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * * 

## <a name="next-steps"></a>後續步驟

深入瞭解[V3 預測端點](luis-migration-api-v3.md)。

了解 [percentage](luis-reference-prebuilt-percentage.md)、[number](luis-reference-prebuilt-number.md) 及 [age](luis-reference-prebuilt-age.md) 實體相關資訊。
