---
title: Azure 串流分析預覽功能
description: 本文列出目前處於預覽狀態的 Azure 串流分析功能。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 59bb866d7a339608555f0bb802e1716eba5d3255
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431583"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 串流分析預覽功能

本文摘要說明目前處於預覽狀態的所有 Azure 串流分析功能。 生產環境不建議使用預覽功能。

## <a name="public-previews"></a>公開預覽

下列功能目前處於公開預覽狀態。 您目前可以利用這些功能，但請勿將其用於生產環境。

### <a name="online-scaling"></a>線上調整

透過線上調整，如果您需要變更 SU 配置，則不需要停止作業。 您可以增加或減少執行中作業的 SU 容量，而不需要停止它。 這是根據客戶對長期執行之要徑任務管線的承諾而定，今天就串流分析提供。 如需詳細資訊，請參閱[設定 Azure 串流分析串流單位](stream-analytics-streaming-unit-consumption.md#configure-stream-analytics-streaming-units-sus)。

### <a name="c-custom-de-serializers"></a>C#自訂還原序列化程式
開發人員可以利用 Azure 串流分析的功能來處理 Protobuf、XML 或任何自訂格式的資料。 您可以在中C#執行[自訂](custom-deserializer-examples.md)的還原序列化程式，然後可以用來將 Azure 串流分析所收到的事件還原序列化。

### <a name="extensibility-with-c-custom-code"></a>自訂C#程式碼的擴充性

在雲端或 IoT Edge 上建立串流分析模組的開發人員可以撰寫或重複C#使用自訂函式，並透過[使用者定義函數](stream-analytics-edge-csharp-udf-methods.md)直接在查詢中叫用這些函數。

### <a name="managed-identity-authentication-with-power-bi"></a>使用 Power BI 的受控識別驗證

Azure 串流分析針對動態儀表板管理體驗，以 Power BI 提供受控識別型驗證的完整支援。

### <a name="anomaly-detection"></a>異常偵測

Azure 串流分析「機器學習模型」除了雙向、緩慢正面和緩慢的負面趨勢偵測之外，還支援*尖峰*和*dip*偵測。 如需詳細資訊，請流覽[Azure 串流分析中的異常偵測](stream-analytics-machine-learning-anomaly-detection.md)。

### <a name="debug-query-steps-in-visual-studio"></a>Visual Studio 中的 Debug 查詢步驟

在適用于 Visual Studio 的 Azure 串流分析工具中進行本機測試時，您可以輕鬆地預覽資料圖表上的中繼資料列集。 

### <a name="local-testing-with-live-data-in-visual-studio-code"></a>在 Visual Studio Code 中使用即時資料進行本機測試

您可以對本機電腦上的即時資料測試查詢，然後再將作業提交至 Azure。 每個測試反復專案平均需要少於二到三秒，因而產生非常有效率的開發流程。

### <a name="visual-studio-code-for-azure-stream-analytics"></a>適用於 Azure 串流分析的 Visual Studio Code

Azure 串流分析作業可以在 Visual Studio Code 中撰寫。 請參閱我們的[VS Code 入門教學](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code)課程。


### <a name="anomaly-detection"></a>異常偵測

Azure 串流分析導入了新的機器學習模型，其可支援「尖峰」和「下降」偵測，以及雙向、緩慢正向趨勢和緩慢負向趨勢偵測。 如需詳細資訊，請流覽[Azure 串流分析中的異常偵測](stream-analytics-machine-learning-anomaly-detection.md)。


### <a name="integration-with-azure-machine-learning"></a>與 Azure Machine Learning 整合

您可以使用 Machine Learning (ML) 函式來調整串流分析作業。 若要深入了解如何在串流分析作業中使用 ML 函式，請造訪[使用 Azure Machine Learning 函式調整串流分析作業](stream-analytics-scale-with-machine-learning-functions.md)。 透過[使用 Azure 串流分析和 Azure Machine Learning 執行情感分析](stream-analytics-machine-learning-integration-tutorial.md)來了解真實案例。


### <a name="live-data-testing-in-visual-studio"></a>在 Visual Studio 中即時測試資料

Azure 串流分析的 Visual Studio 工具加強了本機測試功能，讓您可以針對雲端來源 (例如，事件中樞或 IoT 中樞) 的即時事件串流查詢進行測試。 了解如何[使用適用於 Visual Studio 的 Azure 串流分析工具在本機測試即時資料](stream-analytics-live-data-local-testing.md)。


### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge 上的 .NET 使用者定義函式

有了 .NET 標準使用者定義函式，您可以在串流管線運作期間執行 .NET Standard 程式碼。 您可以建立簡單的 C# 類別，或匯入完整的專案和程式庫。 Visual Studio 支援完整的撰寫和偵錯體驗。 如需詳細資訊，請造訪[針對 Azure 串流分析 Edge 作業開發 .NET Standard 使用者定義的函式](stream-analytics-edge-csharp-udf-methods.md)。

## <a name="other-previews"></a>其他預覽

下列功能也會在要求時提供預覽。

### <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>使用由 Azure Machine Learning 管理的自訂 ML 模型進行即時高效能評分

Azure 串流分析藉由運用由 Azure Machine Learning 管理的自訂預先定型 Machine Learning 模型，並使用工作流程裝載于 Azure Kubernetes Service （AKS）或 Azure 容器實例（ACI），來支援高效能的即時評分這不需要您撰寫程式碼。 [註冊](https://aka.ms/asapreview1)預覽版

### <a name="support-for-azure-stack"></a>Azure Stack 的支援
這項功能會在 Azure IoT Edge 執行時間上啟用，利用自訂的 Azure Stack 功能，例如本機輸入的原生支援，以及在 Azure Stack 上執行的輸出（例如事件中樞、IoT 中樞、Blob 儲存體）。 這種新的整合可讓您建立混合式架構，以便分析接近其產生位置的資料，進而降低延遲並將深入解析發揮到極致。
這項功能可讓您建立混合式架構，以便分析接近其產生位置的資料，降低延遲並將深入解析發揮到極致。 您必須[註冊](https://aka.ms/asapreview1)此預覽版。
