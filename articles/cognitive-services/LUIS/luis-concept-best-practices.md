---
title: 建立 LUIS 應用程式的最佳作法
titleSuffix: Azure Cognitive Services
description: 瞭解最佳作法，以取得 LUIS 應用程式模型的最佳結果。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280917"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>建立語言理解（LUIS）應用程式的最佳作法
使用應用程式撰寫流程來建立 LUIS 應用程式： 

* 組建語言模型（意圖和實體）
* 新增幾個定型範例語句（每個意圖15-30 個）
* 發佈至端點
* 從端點測試 

[發佈](luis-how-to-publish-app.md)您的應用程式之後，請使用開發生命週期，從端點新增功能、發佈和測試。 請不要藉由新增更多範例語句來開始下一個撰寫週期，因為這不會讓 LUIS 以真實世界的使用者語句來學習您的模型。 

請不要展開語句，直到範例和端點語句的目前集合傳回自信的高預測分數。 使用[主動式學習](luis-concept-review-endpoint-utterances.md)來改善分數。 




## <a name="do-and-dont"></a>建議事項和避免事項
以下清單包含 LUIS 應用程式的最佳做法：

|建議事項|避免事項|
|--|--|
|[定義不同的意圖](#do-define-distinct-intents)<br>[將描述項新增至意圖](#do-add-descriptors-to-intents) |[將許多範例語句新增至意圖](#dont-add-many-example-utterances-to-intents)<br>[使用幾個或簡單的實體](#dont-use-few-or-simple-entities) |
|[找出每個意圖的太攏統與太特定之間的最佳點](#do-find-sweet-spot-for-intents)|[使用 LUIS 作為定型平台](#dont-use-luis-as-a-training-platform)|
|[使用版本反復組建您的應用程式](#do-build-your-app-iteratively-with-versions)<br>[模型分解的組建實體](#do-build-for-model-decomposition)|[新增許多相同格式的範例語句而忽略其他格式](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[在稍後的反復專案中新增模式](#do-add-patterns-in-later-iterations)|[混合意圖和實體的定義](#dont-mix-the-definition-of-intents-and-entities)|
|[讓您的語句在所有意圖之間達到平衡](#balance-your-utterances-across-all-intents)，除了 None 意圖之外。<br>[將範例語句新增至 None 意圖](#do-add-example-utterances-to-none-intent)|[建立具有所有可能值的描述項](#dont-create-descriptors-with-all-the-possible-values)|
|[利用主動式學習的建議功能](#do-leverage-the-suggest-feature-for-active-learning)|[新增太多模式](#dont-add-many-patterns)|
|[使用批次測試來監視應用程式的效能](#do-monitor-the-performance-of-your-app)|[新增每一個範例語句都進行定型和發佈](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>請務必定義不同的意圖
請確定每個意圖的詞彙都僅適用於該意圖，而未與不同的意圖重疊。 例如，如果您想要有一個處理旅遊安排 (例如航班和飯店) 的應用程式，則可以選擇讓這些主體區域成為語句內具有特定資料實體的個別意圖或相同意圖。

如果兩個意圖之間的詞彙相同，請將意圖結合，然後使用實體。 

請思考一下以下的範例語句：

|範例語句|
|--|
|預訂班機|
|預訂飯店|

`Book a flight` 和 `Book a hotel` 使用相同的 `book a `詞彙。 此格式相同，因此它應該是使用不同單字 `flight` 的相同意圖，並 `hotel` 為已解壓縮的實體。 

## <a name="do-add-descriptors-to-intents"></a>將描述項新增至意圖

描述項有助於說明意圖的功能。 描述項可以是對該意圖而言非常重要的單字片語清單，或是對該意圖很重要的實體。 

## <a name="do-find-sweet-spot-for-intents"></a>請務必找出意圖的最佳點
請使用來自 LUIS 的預測資料來判斷您的意圖是否重疊。 重疊的意圖會混淆 LUIS。 結果會造成最高分的意圖太接近另一個意圖。 由於 LUIS 不會每次都使用完全相同的資料路徑來進行定型，因此重疊的意圖有可能在定型中成為第一或第二。 您會希望每個意圖的語句分數都儘量拉開，以便避免發生這類不確定情況。 良好的意圖區別應該每次都產生預期的最高分意圖。 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>使用版本反復建立您的應用程式

每個撰寫循環都應該在新[版本](luis-concept-version.md) (從現有版本複製) 之內。 

## <a name="do-build-for-model-decomposition"></a>執行模型分解的組建

模型分解具有一般的處理常式：

* 根據用戶端應用程式的使用者意圖建立**意圖**
* 根據真實世界使用者輸入新增15-30 範例語句
* 在範例語句中標記最上層資料概念
* 將資料概念分成子元件
* 將描述項（功能）新增至子元件
* 將描述項（功能）新增至意圖 

當您建立意圖並新增範例語句之後，下列範例會說明實體分解。 

一開始先找出您想要在語句中解壓縮的完整資料概念。 這是您機器學習的實體。 然後將片語分解成其部分。 這包括識別子元件（做為實體），以及描述項和條件約束。 

例如，如果您想要解壓縮位址，則可以 `Address`呼叫最上層機器學習的實體。 建立位址時，請識別其部分子元件，例如街道位址、城市、州和郵遞區號。 

藉由將郵遞區號**限制**為正則運算式，繼續分解這些元素。 將街道位址分解成街道號碼的幾個部分（使用預先建立的號碼）、街道名稱和街道類型。 街道類型可**與描述項清單（** 例如，路、圓形、道路和 lane）一併說明。

V3 撰寫 API 允許模型分解。 

## <a name="do-add-patterns-in-later-iterations"></a>在稍後的反復專案中新增模式

在新增[模式](luis-concept-patterns.md)之前，您應該先瞭解應用程式的運作方式，因為模式的加權會高於範例語句，而且會扭曲信賴度。 

一旦您瞭解應用程式的行為，請新增其適用于您應用程式的模式。 您不需要在每個[反復](luis-concept-app-iteration.md)專案中加入它們。 

在您的模型設計開始時，並不會有任何傷害，但是在使用語句測試模型之後，會更容易看到每個模式如何變更模型。 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>跨所有意圖平衡您的語句

為了讓 LUIS 預測能正確，每個意圖的範例語句數量必須相對相等 (None 意圖除外)。 

如果您有一個包含 100 個範例語句的意圖和一個包含 20 個範例語句的意圖，100 個語句的意圖會有較高的預測評等。  

## <a name="do-add-example-utterances-to-none-intent"></a>請務必將範例語句新增至 None 意圖

此意圖是回溯意圖，表示您應用程式以外的所有專案。 請在您 LUIS 應用程式的其餘部分，每 10 個範例語句中，便將一個範例語句新增至 None 意圖。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>請務必利用主動式學習的建議功能

請定期使用[主動式學習](luis-how-to-review-endpoint-utterances.md)的**檢閱端點語句**，而不要將更多範例語句新增至意圖。 由於應用程式會不斷接收端點語句，因此這份清單會不斷成長並變更。

## <a name="do-monitor-the-performance-of-your-app"></a>請務必監視您應用程式的效能

請使用[批次測試](luis-concept-batch-test.md)集合來監視預測準確性。 

保留一組不是用來作為[範例語句](luis-concept-utterance.md)或端點語句的個別語句。 持續針對測試集改善應用程式。 調適測試集以反映真實的使用者語句。 使用此測試集來評估應用程式的每個反覆項目或版本。 

## <a name="dont-add-many-example-utterances-to-intents"></a>請勿將許多範例語句新增至意圖

應用程式發佈之後，只會在開發生命週期程式中新增來自主動式學習的語句。 如果語句太類似，則請新增模式。 

## <a name="dont-use-few-or-simple-entities"></a>不使用幾個或簡單的實體

實體是針對資料的提取和預測所建立。 每個意圖都必須具有機器學習的實體來描述意圖中的資料，這點很重要。 即使您的用戶端應用程式不需要使用已解壓縮的實體，這也有助於 LUIS 預測意圖。 

## <a name="dont-use-luis-as-a-training-platform"></a>請勿使用 LUIS 作為定型平台

LUIS 是語言模型定義域特定的。 不是用來作為一般的自然語言訓練平台使用。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>請勿新增許多相同格式的範例語句而忽略其他格式

LUIS 會預期意圖的語句中有所變化。 語句可以在改變的同時仍保有相同的整體意義。 變化可以包括語句長度、單字選擇，以及單字位置。 

|請勿使用相同的格式|請務必使用變化格式|
|--|--|
|購買一張到西雅圖的機票<br>購買一張到巴黎的機票<br>購買一張到奧蘭多的機票|購買 1 張到西雅圖的機票<br>預約兩個下週一到巴黎的紅眼航班機位<br>我想要預訂 3 張到奧蘭多的春假機票|

第二欄使用不同的動詞 (購買、預約、預訂)、不同的量詞 (1、兩、3) 及不同的單字排列，但全都具有購買旅遊機票的相同意圖。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>請勿混合意圖和實體的定義

請針對您 Bot 將執行的任何動作建立意圖。 使用實體作為促成該動作的參數。 

若為將會預訂航班的 bot，請建立**BookFlight**意圖。 請勿為每個航空公司或每個目的地都建立意圖。 請使用這些資料片段作為[實體](luis-concept-entity-types.md)，然後在範例語句中標示它們。 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>請勿建立具有所有可能值的描述項

提供描述項[片語清單](luis-concept-feature.md)中的幾個範例，而不是每個字組。 LUIS 會將內容一般化並納入考量。 

## <a name="dont-add-many-patterns"></a>請勿新增許多模式

請勿新增太多[模式](luis-concept-patterns.md)。 LUIS 旨在透過更少的範例快速學習。 請勿不必要地讓系統多載。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>請勿對每一個範例語句都進行定型和發佈

請新增 10 或 15 個語句之後，再進行定型和發佈。 這將可讓您了解對預測準確性的影響。 新增單一語句對分數可能不會有明顯的影響。 

## <a name="next-steps"></a>後續步驟

* 了解如何在 LUIS 應用程式中[規劃您的應用程式](luis-how-plan-your-app.md)。
