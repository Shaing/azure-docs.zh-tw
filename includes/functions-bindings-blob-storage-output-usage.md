---
title: 包含檔案
description: 包含檔案
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642093"
---
您可以系結至下列類型來寫入 blob:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> 在 *function.json* 或 `FileAccess.Read` C# 類別庫中需要 "in" 繫結 `direction`。 不過，您可以使用執行階段提供的容器物件來執行寫入作業，例如將 blob 上傳至容器。

<sup>2</sup> 在 *function.json* 或 `FileAccess.ReadWrite` C# 類別庫中需要 "inout" 繫結 `direction`。

如果您嘗試繫結至其中一個儲存體 SDK 類型，並出現錯誤訊息，請確定您已參考[正確的儲存體 SDK 版本](#azure-storage-sdk-version-in-functions-1x)。

在非同步函式中，使用傳回值或 `IAsyncCollector`，而不是 `out` 參數。

由於會將整個 blob 內容載入記憶體中，因此只有在 Blob 大小很小時才建議繫結至 `string` 或 `Byte[]`。 一般而言，最好使用 `Stream` 或 `CloudBlockBlob` 類型。 如需詳細資訊，請參閱本文稍早的[並行存取和記憶體使用量](#trigger---concurrency-and-memory-usage)。