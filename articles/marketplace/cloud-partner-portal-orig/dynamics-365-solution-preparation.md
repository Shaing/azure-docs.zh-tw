---
title: Dynamics 365 解決方案準備
description: 用於封裝、安裝和解除安裝元件的架構
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricarod.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: bcb3bb63f305aeb98efda3baf0f6661bd7f67a7c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824237"
---
# <a name="dynamics-365-solution-preparation"></a>Dynamics 365 解決方案準備

Dynamics 365 解決方案系統是用於封裝、安裝和解除安裝元件的架構，提供特定的商務功能。 ISV 和其他 Microsoft Dynamics 365 合作夥伴會使用解決方案散發他們建立的延伸模組。

如果您是現有的 Dynamics 365 (xRM) ISV，您很可能已經建立受控的方案，且有 solution.zip 檔案。 在您的解決方案中，請確定 [顯示名稱] 和 [描述] 欄位反映了您想讓客戶看見的內容。 這些會顯示在 CRM Online 系統管理中心。

![CRMScreenShot1](media/CRMScreenShot1.png)

_**注意：** 在接下來的套件範例中，我們會假設方案名稱是 "SampleSolution.zip"_

若您是新的 ISV，您前往此處獲得關於建立解決方案的詳細資料：[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

如果您的解決方案需要支援的資料：

* 在您的測試環境中建立範例資料
* 使用 Configuration Migration 工具為您的資料建立含有比較規則的結構描述。
* 使用您的專案檔儲存組態結構描述。 若您更新組態資料，稍後會需要此項目。
* 匯出您的組態資料。 請務必指定對您匯出的內容有意義的匯出檔案名稱。
