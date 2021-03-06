---
title: 線性回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用線性回歸模組，以建立要在管線中使用的線性回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 14fe7fff85c7aecd3f98843794f5057cf26fc88d
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548436"
---
# <a name="linear-regression-module"></a>線性回歸模組
本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來建立要在管線中使用的線性回歸模型。  線性回歸會嘗試建立一或多個獨立變數與數值結果或相依變數之間的線性關聯性。 

您可以使用此模組來定義線性回歸方法，然後使用加上標籤的資料集來定型模型。 然後，定型的模型就可用來進行預測。

## <a name="about-linear-regression"></a>關於線性回歸

線性回歸是常見的統計方法，已在機器學習中採用，並使用許多新方法來調整行和測量錯誤。 在最基本的意義中，回歸是指數值目標的預測。 當您想要基本預測工作的簡單模型時，線性回歸仍然是不錯的選擇。 線性回歸通常也適用于缺少複雜性的高維度、稀疏資料集。

除了線性回歸以外，Azure Machine Learning 支援各種不同的回歸模型。 不過，「回歸」一詞可以鬆散地解讀，而其他工具中所提供的一些回歸類型則不受支援。

+ 傳統回歸問題牽涉到單一獨立變數和相依變數。 這稱為「*簡單回歸*」。  此模組支援簡單的回歸。

+ *多個線性回歸*牽涉到兩個或多個參與單一相依變數的獨立變數。 使用多個輸入來預測單一數值結果的問題，也稱為*多變數線性回歸*。

    **線性回歸**模組可以解決這些問題，就像大多數的其他回歸模組一樣。

+ *多重標籤回歸*是在單一模型中預測多個相依變數的工作。 例如，在多標籤羅吉斯迴歸，一個範例可以指派給多個不同的標籤。 （這與在單一類別變數中預測多個層級的工作不同）。

    Azure Machine Learning 中不支援這種類型的回歸。 若要預測多個變數，請為每個您想要預測的輸出建立個別的學習模組。

多年來，統計學家一直在開發日益先進的回歸方法。 即使是線性回歸也是如此。 此模組支援兩種方法來測量錯誤，並配合迴歸線：一般最小平方方法和梯度下降。

- **梯度下降**是一種方法，可將模型定型程式的每個步驟中的錯誤量降到最低。 梯度下降有許多種變異形式，且其各種學習問題已經過廣泛的研究，而最佳化。 如果您針對**方案方法**選擇此選項，您可以設定各種不同的參數來控制步驟大小、學習速率等等。 此選項也支援使用整合式參數清理。

- **一般最小平方**是線性回歸中最常使用的其中一種技術。 例如，最小平方是適用于 Microsoft Excel 的分析工具庫中所使用的方法。

    普通最小平方會使用損失函數，計算實際值與預測線之差距的平方和以求出誤差，並藉由將平方誤差最小化來配適模型。 這個方法會假設輸入和相依變數之間具有強式線性關聯性。

## <a name="configure-linear-regression"></a>設定線性回歸

此模組支援兩種用於調整回歸模型的方法，並具有不同的選項：

+ [使用線上梯度下降建立回歸模型](#bkmk_GradientDescent)

    對於較為複雜的模型，或是沒有足夠的定型資料供給定的變數數目使用的模型，梯度下降會是較理想的損失函數。



+ [使用一般最小平方來配合回歸模型](#bkmk_OrdinaryLeastSquares)

    對於小型資料集，最好選取 [普通最小平方]。 這應該會為 Excel 提供類似的結果。

## <a name="bkmk_OrdinaryLeastSquares"></a>使用一般最小平方來建立回歸模型

1. 將**線性回歸模型**模組加入至您在設計工具中的管線。

    您可以在 [ **Machine Learning** ] 類別目錄中找到此模組。 展開 [**初始化模型**]，展開 [**回歸**]，然後將 [**線性回歸模型**] 模組拖曳至您的管線。

2. 在 [**屬性**] 窗格的 [**方案方法**] 下拉式清單中，選取 [**普通最小平方**]。 這個選項會指定用來尋找迴歸線的計算方法。

3. 在 [ **l2 正規化權數**] 中，輸入要用來做為 l2 正規化權數的值。 建議您使用非零值來避免過度學習。

     若要深入瞭解正規化如何影響模型的調整，請參閱這篇文章：[適用于 Machine Learning 的 L1 和 L2 正規化](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. 如果您想要查看截距的字詞，請選取 [**包含攔截詞彙**] 選項。

    如果您不需要檢查回歸公式，請取消選取此選項。

5. 針對 [**亂數字種子**]，您可以選擇性地輸入值來植入模型所使用的亂數產生器。

    如果您想要在相同管線的不同執行之間維持相同的結果，使用種子值會很有用。 否則，預設會使用系統時鐘的值。


7. 將[訓練模型](./train-model.md)模組新增至您的管線，並連接已加上標籤的資料集。

8. 執行管道。

## <a name="results-for-ordinary-least-squares-model"></a>一般最小平方模型的結果

定型完成後：


+ 若要進行預測，請將定型的模型連接到 [[評分模型](./score-model.md)] 模組，以及新值的資料集。 


## <a name="bkmk_GradientDescent"></a>使用線上梯度下降建立回歸模型

1. 將**線性回歸模型**模組加入至您在設計工具中的管線。

    您可以在 [ **Machine Learning** ] 類別目錄中找到此模組。 展開 [**初始化模型**]，展開 [**回歸**]，然後將 [**線性回歸模型**] 模組拖曳至您的管線

2. 在 [**屬性**] 窗格的 [**方案方法**] 下拉式清單中，選擇 [**線上梯度下降**] 做為用來尋找迴歸線的計算方法。

3. 針對 [**建立培訓者模式]** ，指出您要使用一組預先定義的參數來定型模型，還是要使用參數清除來將模型優化。

    + **單一參數**：如果您知道要如何設定線性回歸網路，您可以提供一組特定值做為引數。

   
4. 針對 [**學習速率**]，指定隨機梯度下降優化工具的初始學習速率。

5. 在 [**定型 Epoch 數**] 中，輸入一個值，指出演算法應該逐一查看範例的次數。 對於只有少數範例的資料集，這個數字應該要大到能夠達到收斂為止。

6. **標準化功能**：如果您已經正規化用來定型模型的數值資料，則可以取消選取此選項。 根據預設，此模組會將所有數值輸入正規化為介於0到1之間的範圍。

    > [!NOTE]
    > 
    > 請記得將相同的正規化方法套用至用於計分的新資料。

7. 在 [ **l2 正規化權數**] 中，輸入要用來做為 l2 正規化權數的值。 建議您使用非零值來避免過度學習。

    若要深入瞭解正規化如何影響模型的調整，請參閱這篇文章：[適用于 Machine Learning 的 L1 和 L2 正規化](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. 如果您想要讓學習速率減少為反復專案進度，請選取 [**減少學習速率**] 選項。  

10. 針對 [**亂數字種子**]，您可以選擇性地輸入值來植入模型所使用的亂數產生器。 如果您想要在相同管線的不同執行之間維持相同的結果，使用種子值會很有用。


12. 新增加上標籤的資料集和其中一個定型模組。

    如果您不使用參數清除，請使用 [[定型模型](train-model.md)] 模組。

13. 執行管道。

## <a name="results-for-online-gradient-descent"></a>線上梯度下降的結果

定型完成後：

+ 若要進行預測，請將定型的模型連接到 [[評分模型](./score-model.md)] 模組，連同新的輸入資料。


## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 