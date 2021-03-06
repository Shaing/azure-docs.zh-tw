---
title: Azure 網際網路分析器疑難排解 |Microsoft Docs
description: Azure Internet Analyzer 的疑難排解參考。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909045"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Azure 網際網路分析器疑難排解

本文包含常見的網際網路分析器問題的疑難排解步驟。

## <a name="azure-portal"></a>Azure 入口網站
**[計分卡] 區段中的 [無法為此計分卡收集足夠的測量]**

請注意：
- 用戶端腳本必須內嵌至**HTTPS**網站。 如果腳本在純文字（**HTTP://** ）或本機（**file://** ）網站中執行，則不會收集度量。
- 只有當網際網路分析器設定檔的用戶端腳本已內嵌至接收實際使用者流量的應用程式時，才會收集測量資料。 綜合流量（例如，Azure WebApp 效能測試）通常不會執行內嵌的 JAVAscript 程式碼，因此不會由該類型的流量產生任何測量。
- 時間序列會每小時產生一次，因此您必須至少等候這段時間，才會顯示新的度量資料。
- 每日會產生計分卡（在每日結束時，UTC 時間）。
- 只有針對選取的篩選組合（測試、時間週期、國家（地區）等等）收集超過100的度量時，才會產生計分卡。

## <a name="next-steps"></a>後續步驟
閱讀 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)
