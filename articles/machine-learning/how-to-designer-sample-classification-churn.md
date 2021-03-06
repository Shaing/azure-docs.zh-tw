---
title: 設計工具：預測流失的範例
titleSuffix: Azure Machine Learning
description: 遵循此分類範例，利用 Azure Machine Learning 設計工具 & 促進式決策樹來預測流失。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311105"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>使用促進式決策樹，利用 Azure Machine Learning 設計工具來預測流失

**設計師範例5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

瞭解如何使用設計工具來建立複雜的機器學習管線，而不需要撰寫任何一行程式碼。

此管線會訓練 2**個雙類別促進式決策樹**分類器，以預測客戶關係管理（CRM）系統的一般工作-客戶流失。 資料值和標籤會分割成多個資料來源，並以匿名的客戶資訊進行加密，不過，我們仍然可以使用設計工具來結合資料集，並使用遮蔽的值來定型模型。

因為您正試著回答「哪一個？」這個問題， 這稱為分類問題，但您可以套用此範例中所示的相同邏輯，以處理任何類型的機器學習服務問題，無論是回歸、分類、叢集等。

以下是此管線的完成圖形：

![管線圖表](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>必要條件

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. 按一下 [範例 5] 將其開啟。 

## <a name="data"></a>資料

此管線的資料來自 KDD 杯2009。 它有50000個數據列和230個功能資料行。 這項工作是針對使用這些功能的客戶，預測變換、appetency 和向上銷售。 如需有關資料和工作的詳細資訊，請參閱[KDD 網站](https://www.kdd.org/kdd-cup/view/kdd-cup-2009)。

## <a name="pipeline-summary"></a>管線摘要

設計工具中的這個範例管線會顯示變換、appetency 和向上銷售的二元分類器預測，這是客戶關係管理（CRM）的一般工作。

首先，部分簡單的資料處理。

- 原始資料集有許多遺漏值。 使用 [**清除遺漏的資料**] 模組，將遺漏的值取代為0。

    ![清除資料集](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- 功能和對應的變換會在不同的資料集中。 使用 [**加入資料行**] 模組，將標籤資料行附加至特徵資料行。 第一個資料行**Col1**是標籤資料行。 從視覺效果的結果中，我們可以看到資料集不對稱。 與正面範例（+ 1）相比，有更多的負面（-1）範例。 我們會在稍後使用**SMOTE**模組來增加不具代表性案例。

    ![加入資料行資料集](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- 使用**分割資料**模組，將資料集分割成定型和測試集。

- 然後使用「促進式決策樹」二進位分類器搭配預設參數來建立預測模型。 為每個工作建立一個模型，也就是每一個模型都可預測向上銷售、appetency 和變換。

- 在管線的右側部分，我們會使用**SMOTE**模組來增加正向範例的百分比。 SMOTE 百分比會設定為100，以將正的範例加倍。 深入瞭解 SMOTE 模組如何與[SMOTE 模組 reference0](algorithm-module-reference/smote.md)搭配運作。

## <a name="results"></a>結果

將 [**評估模型**] 模組的輸出視覺化，以查看測試集上模型的效能。 

![評估結果](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 您可以移動 [**臨界值**] 滑杆，並查看二元分類工作的計量變更。 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

探索適用于設計工具的其他範例：

- [範例 1-回歸：預測汽車的價格](how-to-designer-sample-regression-automobile-price-basic.md)
- [範例 2-回歸：比較汽車價格預測的演算法](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [範例 3-使用特徵選取進行分類：收入預測](how-to-designer-sample-classification-predict-income.md)
- [範例 4-分類：預測信用風險（區分成本）](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [範例 6-分類：預測航班延誤](how-to-designer-sample-classification-flight-delay.md)
- [範例 7-文字分類：維琪百科 SP 500 資料集](how-to-designer-sample-text-classification.md)
