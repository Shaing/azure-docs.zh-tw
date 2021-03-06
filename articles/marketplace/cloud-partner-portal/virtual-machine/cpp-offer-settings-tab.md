---
title: Azure Marketplace 的 Cloud Partner 入口網站中的 [虛擬機器供應專案設定] 索引標籤
description: 說明用來建立 Microsoft Azure Marketplace 虛擬機器供應項目的供應項目設定索引標籤。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 6f7b90f6b02999869026db24836091233692143c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824450"
---
# <a name="virtual-machine-offer-settings-tab"></a>虛擬機器供應項目設定索引標籤

虛擬機器的**新增供應項目**頁面會在名為**供應項目設定**的第一個索引標籤開啟。  

![虛擬機器的新供應項目頁面](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>供應項目設定欄位

在 [**供應專案設定**] 索引標籤中，您必須提供下欄欄位。  欄位名稱上附加星號 (*) 表示為必填欄位。 

|  **欄位**       |     **說明**                                                          |
|  ---------       |     ---------------                                                          |
| **供應專案識別碼\***   | 供應項目的唯一識別碼 (位於發行設定檔中)。 此識別碼會顯示在產品 URL、Azure Resource Manager 範本和計費報告中。 識別碼長度上限為 50 個字元，只能包含小寫英數字元和連字號 (-)，但不能以連字號結尾。 供應項目上線之後即無法變更此欄位。 <br> 例如，若 Contoso 發佈了一個識別碼為 **sample-vm** 的供應項目，其會收到指派的 Azure Marketplace URL`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`。 |
| **發行者\***  | 您的組織在 Microsoft Azure Marketplace 中的唯一識別碼。 您的所有供應項目都需要連結到您的發行者識別碼。 供應項目儲存後，不可再修改此欄位內容。 |
| **名稱\***       | 您的供應項目顯示名稱。 此名稱會顯示在 Microsoft Azure Marketplace 和 Cloud Partner 入口網站中。 它最多不能超過 50 個字元。 建議在產品中包含明顯的品牌名稱。 請勿在此包含您的組織名稱，除非這是行銷的方式。 如果您要透過其他網站和出版物行銷此供應項目，請確保名稱在所有出版物上都相同。 |
|   |   |
 
提供所有欄位之後，請按一下 [**儲存**]。 


## <a name="next-steps"></a>後續步驟

在下一個索引標籤中，您將新增 [SKU](./cpp-skus-tab.md) 至供應項目中。
