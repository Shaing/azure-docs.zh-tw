---
title: 包含檔案
description: 包含檔案
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c3677e7897498aa06d7bd547988ad4dc0326f39b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174068"
---
## <a name="enable-event-grid-resource-provider"></a>啟用事件格線資源提供者

如果您先前在 Azure 訂用帳戶中從未使用過事件方格，可能必須註冊事件方格資源提供者。

在 Azure 入口網站中：

1. 選取 **訂用帳戶** 。
1. 選取您用於事件方格的訂用帳戶。
1. 在 [設定]  下，選取 [資源提供者]  。
1. 尋找 **Microsoft.EventGrid**。
1. 如果未註冊，請選取 [註冊]  。 

可能需要一點時間才能完成註冊。 選取 [重新整理]  來更新狀態。 當 [狀態]  是 [已註冊]  時，代表已準備好繼續進行。