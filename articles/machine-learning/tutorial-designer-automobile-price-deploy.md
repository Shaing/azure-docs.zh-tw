---
title: 教學課程：透過設計工具部署機器學習模型
titleSuffix: Azure Machine Learning
description: 本教學課程說明如何在 Azure Machine Learning 設計工具中建置預測性分析解決方案 (預覽)。 使用拖放模組進行機器學習模型的定型、評分和部署。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 470e633b2c64283db9b6a5b1cd777a6a054dad16
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719784"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>教學課程：透過設計工具部署機器學習模型 (預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

您可以部署在[教學課程第一部分](tutorial-designer-automobile-price-train-score.md)中開發的預測模型，讓其他人有機會加以使用。 在第一部分中，您已將模型定型。 現在，是時候根據使用者輸入來產生新預測了。 在教學課程的這個部分中，您會：

> [!div class="checklist"]
> * 建立即時推斷管線。
> * 建立推斷叢集。
> * 部署即時端點。
> * 測試即時端點。

## <a name="prerequisites"></a>Prerequisites

完成[教學課程的第一個部分](tutorial-designer-automobile-price-train-score.md)，了解如何在設計工具中定型和評分機器學習模型。

## <a name="create-a-real-time-inference-pipeline"></a>建立即時推斷管線

若要部署您的管線，您必須先將訓練管線轉換成即時推斷管線。 此程序會移除訓練模組，並新增 Web 服務輸入和輸出以處理要求。

### <a name="create-a-real-time-inference-pipeline"></a>建立即時推斷管線

1. 在管線畫布上方，選取 [建立推斷管線]   > [即時推斷管線]  。

    您的管線此時應會顯示如下： 

   ![此螢幕擷取畫面顯示管線在做好部署準備後的預期組態](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    當您選取 [建立推斷管線]  時，會發生若干情況：
    
    * 定型的模型會儲存為模組選擇區中的**資料集**模組。 您可以在 [我的資料集]  下方找到該項目。
    * 訓練模組 (例如**訓練模型**和**分割資料**) 會被移除。
    * 儲存的定型模型會加回管線中。
    * 會新增 **Web 服務輸入**和 **Web 服務輸出**模組。 這些模組會顯示使用者資料將在何處進入管線，以及資料會在何處傳回。

    > [!NOTE]
    > 根據預設，**Web 服務輸入**會預期與用來建立預測管線的訓練資料相同的資料結構描述。 在此情況下，價格會包含在結構描述中。 不過，在預測期間並不會以價格作為因素。
    >

1. 選取 [執行]  ，並使用您在第一部分中使用的相同計算目標和實驗。

1. 選取 [部署]  。

## <a name="create-an-inferencing-cluster"></a>建立推斷叢集

在出現的對話方塊中，您可以從任何現有的 Azure Kubernetes Service (AKS) 叢集進行選取，以將模型部署至其中。 如果您沒有 AKS 叢集，請使用下列步驟建立一個。

1. 在出現的對話方塊中選取 [計算]  ，以移至 [計算]  頁面。

1. 在導覽功能區中，選取 [推斷叢集]   > [+ 新增]  。

    ![顯示如何移至 [新增推斷叢集] 窗格的螢幕擷取畫面](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. 在推斷叢集窗格中，設定新的 Kubernetes 服務。

1. 輸入 aks-compute  作為**計算名稱**。
    
1. 選取附近的適用區域作為**區域**。

1. 選取 [建立]  。

    > [!NOTE]
    > 建立新的 AKS 服務需要約 15 分鐘。 您可以在 [推斷叢集]  頁面上查看佈建狀態。
    >

## <a name="deploy-the-real-time-endpoint"></a>部署即時端點

在您的 AKS 服務完成佈建後，請回到即時推斷管線以完成部署。

1. 選取畫布上方的 [部署]  。

1. 選取 [部署新的即時端點]  。 

1. 選取您建立的 AKS 叢集。

1. 選取 [部署]  。

    ![顯示如何設定新即時端點的螢幕擷取畫面](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    完成部署之後，畫布上方會出現成功通知。 這可能需要幾分鐘的時間。

## <a name="test-the-real-time-endpoint"></a>測試即時端點

部署完成後，您即可移至 [端點]  頁面，以測試您的即時端點。

1. 在 [端點]  頁面上，選取您部署的端點。

    ![顯示即時端點索引標籤的螢幕擷取畫面，其中醒目提示了最近建立的端點](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. 選取 [測試]  。

1. 您可以手動輸入測試資料或使用自動填入的範例資料，然後選取 [測試]  。

    入口網站會將測試要求提交至端點，並顯示結果。 雖然對輸入資料會產生價格值，但該值並不會用來產生預測值。

    ![顯示如何測試即時端點的螢幕擷取畫面，其中醒目提示了價格的評分標籤](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解在如何設計工具中建立、部署和使用機器學習模型的關鍵步驟。 若要深入了解如何使用設計工具來解決其他類型的問題，請參閱我們的其他範例管線。

> [!div class="nextstepaction"]
> [信用風險分類範例](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
