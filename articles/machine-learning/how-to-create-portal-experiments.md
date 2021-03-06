---
title: 組建 & 部署自動化的 ML 模型
titleSuffix: Azure Machine Learning
description: 在 Azure Machine Learning studio 中建立、管理和部署自動化的機器學習實驗。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: tsikiksr
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 11/04/2019
ms.openlocfilehash: 808d7ac7ded9b250e0835da51b6b547c05c622a9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720396"
---
# <a name="create-explore-and-deploy-automated-machine-learning-experiments-with-azure-machine-learning-studio"></a>使用 Azure Machine Learning studio 建立、探索及部署自動化的機器學習實驗
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

 在本文中，您將瞭解如何在不需使用任何程式碼的情況下，在 Azure Machine Learning studio 中建立、探索及部署自動化的機器學習實驗。 自動化機器學習服務會自動化選取最佳演算法以用於特定資料的程式，因此您可以快速產生機器學習模型。 [深入瞭解自動化機器學習](concept-automated-ml.md)服務。

 如果您偏好更以程式碼為基礎的經驗，您也可以使用[AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，[在 Python 中設定自動化機器學習實驗](how-to-configure-auto-train.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 具有**Enterprise edition**類型的 Azure Machine Learning 工作區。 請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。  若要將現有的工作區升級為 Enterprise edition，請參閱[升級至 enterprise edition](how-to-manage-workspace.md#upgrade)。

## <a name="get-started"></a>開始使用

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com)。 

1. 選取您的訂用帳戶和工作區。 

1. 導覽至左窗格。 在 [**作者**] 區段下選取 [**自動化 ML** ]。

[![Azure Machine Learning Studio 導覽窗格](media/how-to-create-portal-experiments/nav-pane.png)](media/how-to-create-portal-experiments/nav-pane-expanded.png)

 如果這是您第一次執行任何實驗，您會看到空白清單和檔連結。 

否則，您會看到最近的自動化機器學習實驗清單，包括以 SDK 建立的測試。 

## <a name="create-and-run-experiment"></a>建立並執行實驗

1. 選取 [ **+ 建立實驗**] 並填入表單。

1. 從儲存體容器中選取資料集，或建立新的資料集。 您可以從本機檔案、web url、資料存放區或 Azure 開放資料集建立資料集。 

    >[!Important]
    > 定型資料的需求：
    >* 資料必須是表格式格式。
    >* 您想要預測的值（目標資料行）必須存在於資料中。

    1. 若要從本機電腦上的檔案建立新的資料集，請選取 **[流覽]** ，然後選取檔案。 

    1. 為您的資料集提供唯一名稱，並提供選擇性的描述。 

    1. 選取 **[下一步]** ，將其上傳至自動使用您的工作區建立的預設儲存體容器，或選擇您要用於實驗的儲存體容器。 

    1. 檢查**設定和預覽**表單的正確性。 表單會根據檔案類型以智慧方式填入。 

        欄位| 說明
        ----|----
        檔案格式| 定義檔案中所儲存資料的版面配置和類型。
        分隔符號| 一或多個字元，用於指定純文字或其他資料流程中個別獨立區域之間的界限。
        編碼| 識別要用來讀取資料集之字元結構描述資料表的位元。
        資料行標題| 指出資料集標題 (如果有的話) 的處理方式。
        Skip rows (略過資料列) | 指出資料集內略過多少資料列 (如果有的話)。
    
        選取 [下一步]。

    1. **架構**表單會根據 [**設定] 和 [預覽**] 表單中的選取專案，以智慧方式填入。 在這裡，設定每個資料行的資料類型，檢查資料行名稱，然後選取要為您的實驗**不包含**的資料行。 
            
        選取 **[下一步]。**

    1. [**確認詳細資料**] 表單是先前在 [**基本資訊**] 和 [**設定] 和 [預覽**] 表單中填入之資訊的摘要。 您也可以選擇使用啟用分析的計算來分析您的資料集。 深入了解[資料分析](#profile)。

        選取 [下一步]。
1. 在新建立的資料集出現之後，加以選取。 您也可以查看資料集的預覽和範例統計資料。 

1. 在 [**設定執行**] 表單上，輸入唯一的實驗名稱。

1. 選取目標資料行;這是您想要對其執行預測的資料行。

1. 選取資料分析和訓練作業的計算。 您現有計算的清單可在下拉式清單中取得。 若要建立新的計算，請遵循步驟7中的指示。

1. 選取 [**建立新的計算**]，以設定此實驗的計算內容。

    欄位|說明
    ---|---
    計算名稱| 輸入可識別您計算內容的唯一名稱。
    虛擬機器大小| 為您的計算選取虛擬機器大小。
    最小/最大節點數 (在 [進階設定] 中)| 若要分析資料，您必須指定一個或多個節點。 輸入您計算的節點數目上限。 AML 計算的預設值是6個節點。
    
    選取 [建立]。 建立新的計算可能需要幾分鐘的時間。

    >[!NOTE]
    > 您的計算名稱會指出您選取/建立的計算是否已啟用程式碼*剖析*。 （如需詳細資訊，請參閱[資料分析](#profile)一節）。

    選取 [下一步]。

1. 在 [工作**類型和設定**] 表單上，選取 [工作類型]： [分類]、[回歸] 或 [預測]。 如需詳細資訊，請參閱[如何定義工作類型](how-to-define-task-type.md)。

    1. 針對分類，您也可以啟用用於文字 featurizations 的深度學習。

    1. 針對預測：
        1. 選取時間資料行：此資料行包含要使用的時間資料。

        1. 選取 [預測範圍]：指出模型能夠預測到未來的時間單位數（分鐘/小時/天/周/月/年）。 需要進一步預測模型，使其變得更不精確。 [深入瞭解預測和水準預測](how-to-auto-train-forecast.md)。

1. 選擇性新增設定：您可以用來更有效控制定型作業的其他設定。 否則會根據實驗選取範圍和資料來套用預設值。 

    其他組態|說明
    ------|------
    主要計量| 用來評分模型的主要度量。 [深入瞭解模型計量](how-to-configure-auto-train.md#explore-model-metrics)。
    自動特製化| 選取以啟用或停用自動化機器學習完成的前置處理。 前置處理包含自動資料清理、準備和轉換來產生綜合功能。 [深入瞭解](#preprocess)前置處理。
    封鎖的演算法| 選取您想要從定型作業中排除的演算法。
    結束準則| 符合上述任一條件時，就會停止定型作業。 <br> *定型作業時間（小時）* ：允許執行訓練作業的時間長度。 <br> *度量分數臨界值*：所有管線的最小度量分數。 這可確保如果您有想要觸達的已定義目標計量，則不需要花費更多時間來進行定型作業。
    驗證| 選取要在定型作業中使用的其中一個交叉驗證選項。 [深入瞭解交叉驗證](how-to-configure-auto-train.md)。
    並行| *並行反覆運算*數上限：要在定型作業中測試的管線數目上限（反覆運算）。 作業不會執行超過指定的反覆運算次數。 <br> *每個反復專案的最大核心*數：選取您要在使用多核心計算時使用的多核心限制。

<a name="profile"></a>

## <a name="data-profiling--summary-stats"></a>資料分析 & 摘要統計資料

您可以在資料集內取得各式各樣的摘要統計資料，以確認您的資料集是否為 ML 備妥。 若是非數值資料行，則只會包含基本統計資料，例如最小值、最大值和錯誤計數。 針對數值資料行，您也可以查看其統計時間和預估分量。 具體而言，我們的資料設定檔包括：

>[!NOTE]
> 具有無關類型的功能會顯示空白專案。

統計資料|說明
------|------
功能| 摘要資料行的名稱。
設定檔| 以推斷的型別為基礎的內嵌視覺效果。 例如，字串、布林值和日期會有數值計數，而小數（數值）則具有近似長條圖。 這可讓您快速瞭解資料的散發。
類型散發| 資料行內類型的內建數值計數。 Null 是其本身的類型，因此此視覺效果適用于偵測奇數或遺漏值。
類型|推斷的資料行類型。 可能的值包括：字串、布林值、日期和小數。
最小值| 資料行的最小值。 如果功能的類型沒有固有的順序（例如布林值），則會出現空白專案。
最大值| 資料行的最大值。 
計數| 資料行中遺漏和不遺失的專案總數。
未遺漏計數| 資料行中未遺漏的專案數。 空字串和錯誤會被視為值，因此它們不會參與「未遺漏計數」。
分量| 每個分量的近似值可讓您瞭解資料的分佈。
平均值| 資料行的算術平均值或平均值。
標準差| 此資料行資料的散佈或變化量的測量單位。
Variance| 量值分佈在此資料行中的值是來自其平均值。 
偏度| 測量此資料行的資料與一般散發的不同之處。
峰度| 測量此資料行的資料與一般散發的比較程度。


<a name="preprocess"></a>

## <a name="advanced-featurization-options"></a>Advanced 特徵化選項

在設定您的實驗時，您可以 `feauturization`啟用 [advanced] 設定。 

|特徵化設定 | 說明 |
| ------------- | ------------- |
|"feauturization" = ' FeaturizationConfig '| 表示應該使用自訂的特徵化步驟。 [瞭解如何自訂特徵化](how-to-configure-auto-train.md#customize-feature-engineering)。|
|"feauturization" = ' off '| 表示不應自動執行特徵化步驟。|
|"feauturization" = ' auto '| 指出在前置處理中，會自動執行下列資料護欄和特徵化步驟。|

|前置處理&nbsp;步驟| 說明 |
| ------------- | ------------- |
|卸除高基數或無變異數特徵|從定型和驗證集卸載這些，包括所有遺漏值的功能、所有資料列中的相同值，或非常高的基數（例如，雜湊、識別碼或 Guid）。|
|插補遺漏值|對於數值特徵，請插補資料行中的平均值。<br/><br/>針對類別特徵，插補最常的值。|
|產生其他特徵|針對日期時間功能：年、月、日、每週的日期、年的日、季、周、小時、分鐘、秒。<br/><br/>針對文字功能：根據 unigrams、bi 字母和三字元-克的詞彙頻率。|
|轉換和編碼 |具有少數唯一值的數值特徵會轉換成類別特徵。<br/><br/>針對低基數類別目錄執行一次熱編碼;適用于高基數、一次熱雜湊編碼。|
|Word 內嵌|文字 featurizer 會使用預先定型的模型，將文字標記的向量轉換成句子向量。 檔中每個單字的內嵌向量會匯總在一起，以產生檔功能向量。|
|目標編碼|針對分類功能，會將每個類別對應至回歸問題的平均目標值，並針對分類問題的每個類別，將其設為類別的機率。 以頻率為基礎的加權和 k 折迭交叉驗證會套用，以減少因稀疏資料類別而造成的對應和雜訊的過度調整。|
|文字目標編碼|若為文字輸入，則會使用具有文字包裝的堆疊線性模型來產生每個類別的機率。|
|辨識項的權數（WoE）|將 WoE 計算為分類資料行與目標資料行相互關聯的量值。 其計算方式是以類別與外類別機率的比率為記錄。 此步驟會輸出每個類別的一個數值特徵資料行，而不需要明確插補遺漏值和極端值處理。|
|叢集距離|在所有數值資料行上訓練 k 表示叢集模型。  輸出 k 個新功能，每個叢集一個新的數值功能，其中包含每個樣本與每個叢集距心的距離。|

### <a name="data-guardrails"></a>資料護欄

自動化機器學習服務提供資料護欄，可協助您識別資料的潛在問題（例如遺漏值、類別不平衡），並協助採取更正動作來改善結果。 有許多最佳做法可供使用，而且可以套用以達成可靠的結果。 

下表說明目前支援的資料護欄，以及使用者在提交實驗時可能會遇到的相關狀態。

Guardrail|狀態|&nbsp;觸發程式的條件&nbsp;
---|---|---
遺漏&nbsp;值&nbsp;插補 |**已** <br> <br> **固定匯率**|    任何輸入&nbsp;資料行中沒有遺漏值 <br> <br> 有些資料行有遺漏值
交叉驗證|**即可**|如果未提供明確的驗證集
高&nbsp;基數&nbsp;功能&nbsp;偵測|  **已** <br> <br>**即可**|   未偵測到高基數功能 <br><br> 偵測到高基數輸入資料行
類別餘額偵測 |**已** <br><br><br>**警示** |類別會在定型資料中進行平衡;如果資料集內的每個類別都有良好的標記法，則會將資料集視為對稱，如樣本的數目和比率所測量 <br> <br> 定型資料中的類別是不平衡
時間序列資料一致性|**已** <br><br><br><br> **固定匯率** |<br> 已分析選取的 {地平線、延遲、滾動視窗} 值，且未偵測到任何可能的記憶體不足問題。 <br> <br>已分析選取的 {水準、延遲、滾動視窗} 值，而且可能會導致您的實驗用盡記憶體。 已關閉 lag 或滾動視窗。

## <a name="run-experiment-and-view-results"></a>執行實驗並查看結果

選取 [**啟動**] 以執行您的實驗。 實驗準備程式可能需要10分鐘的時間。 定型作業可能需要額外2-3 分鐘的時間，每個管線才會完成執行。

### <a name="view-experiment-details"></a>檢視實驗詳細資料

>[!NOTE]
> 定期**選取**[重新整理] 以查看執行的狀態。 

[**執行詳細資料**] 畫面會開啟至 [**詳細**資料] 索引標籤。此畫面會顯示實驗執行的摘要，包括**執行狀態**。 

[**模型**] 索引標籤包含依據度量分數所建立的模型清單。 根據預設，以所選計量為最高分數的模型會在清單頂端。 當定型作業嘗試多個模型時，會將它們加入清單中。 使用此來快速比較目前為止所產生之模型的計量。

[![執行詳細資料儀表板](media/how-to-create-portal-experiments/run-details.png)](media/how-to-create-portal-experiments/run-details-expanded.png#lightbox)

### <a name="view-training-run-details"></a>View 定型執行詳細資料

向下切入任何已完成的模型，以查看定型執行詳細資料，例如在 [**視覺效果**] 索引標籤上的 [**模型詳細資料**] 索引標籤或效能圖表上執行計量。[深入瞭解圖表](how-to-understand-automated-ml.md)。

[![反復專案詳細資料](media/how-to-create-portal-experiments/iteration-details.png)](media/how-to-create-portal-experiments/iteration-details-expanded.png)

## <a name="deploy-your-model"></a>部署模型

當您手邊擁有最佳模型之後，就可以將它部署為 web 服務，以預測新的資料。

自動化 ML 可協助您部署模型，而不需要撰寫程式碼：

1. 您有幾個部署選項。 

    + 選項1：若要部署最佳模型（根據您所定義的度量準則），請從 [詳細資料] 索引標籤選取 [部署最佳模型]。

    + 選項2：若要從此實驗部署特定模型反復專案，請向下切入模型以開啟其 [模型詳細資料] 索引標籤，然後選取 [部署模型]。

1. 填入 [**部署模型**] 窗格。

    欄位| 值
    ----|----
    名稱| 為您的部署輸入唯一的名稱。
    說明| 輸入描述，以更清楚地識別此部署的用途。
    計算類型| 選取您想要部署的端點類型： *Azure Kubernetes Service （AKS）* 或*Azure 容器實例（ACI）* 。
    名稱| *僅適用于 AKS：* 選取您想要部署的 AKS 叢集名稱。
    啟用驗證 | 選取即可允許以權杖或金鑰為基礎的驗證。
    使用自訂部署資產| 如果您想要上傳自己的評分腳本和環境檔案，請啟用此功能。 [深入瞭解評分腳本](how-to-deploy-and-where.md#script)。

    >[!Important]
    > 檔案名的長度必須是32個字元，而且必須以英數位元開頭和結尾。 可能包含虛線、底線、點和之間的英數位元。 不允許使用空格。

    [ *Advanced* ] 功能表提供預設的部署功能，例如[資料收集](how-to-enable-app-insights.md)和資源使用量設定。 如果您想要覆寫這些預設值，請在此功能表中執行此動作。

1. 選取 [部署]。 部署可能需要大約20分鐘的時間才能完成。

現在您已有可運作的 web 服務來產生預測！ 您可以從[Power BI 內建 Azure Machine Learning 支援](how-to-consume-web-service.md#consume-the-service-from-power-bi)來查詢服務，藉以測試預測。

## <a name="next-steps"></a>後續步驟

* 嘗試[使用 Azure Machine Learning 建立您的第一個自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)的端對端教學課程。 
* [深入瞭解自動化機器學習](concept-automated-ml.md)和 Azure Machine Learning。
* [瞭解自動化的機器學習結果](how-to-understand-automated-ml.md)。
* [瞭解如何使用 web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)。
