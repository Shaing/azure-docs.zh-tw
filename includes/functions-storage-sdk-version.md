---
title: 包含檔案
description: 包含檔案
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608120"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x 中的 Azure Storage SDK 版本

在 Functions 1.x 中，儲存體觸發程序和繫結會使用 Azure 儲存體 SDK 7.2.1 版 ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet 套件)。 如果參考不同版本的儲存體 SDK，且繫結至函式簽章中的儲存體 SDK 類型，則 Functions 執行階段可能會報告它無法繫結至該類型。 解決方案是確定您的專案參考 [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)。
