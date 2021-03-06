---
title: 使用設計工具中的協助工具功能
titleSuffix: Azure Machine Learning
description: 瞭解可在設計工具中使用的鍵盤快速鍵和螢幕閱讀程式協助工具功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 7d5c8047fea8e9081de08b582c537711714ea463
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314227"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer"></a>使用鍵盤來使用 Azure Machine Learning 設計工具

瞭解如何使用鍵盤和螢幕閱讀程式來使用 Azure Machine Learning 的設計工具。 如需 Azure 入口網站中任何地方都能使用的鍵盤快速鍵清單，請參閱[Azure 入口網站中的鍵盤快捷](../azure-portal/azure-portal-keyboard-shortcuts.md)方式。

此工作流程已使用 [[朗讀](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator)程式] 和 [ [JAWS](https://www.freedomscientific.com/products/software/jaws/)] 進行測試，但它應該與其他標準螢幕讀取器搭配使用。

## <a name="navigate-the-pipeline-graph"></a>流覽管線圖形

管線圖表會組織成清單清單。 最上層模組清單包含管線中的所有模組。 [模組] 清單中的每個專案都包含一個描述其所有連線的連接清單。 

1. 在模組清單中，使用方向鍵來切換模組。
1. 使用 [索引標籤] 開啟目的模組的連接清單。
1. 使用方向鍵在模組的連線通訊埠之間切換。
1. 使用 "G" 移至目的模組。

## <a name="edit-the-pipeline-graph"></a>編輯管線圖形

### <a name="add-a-module-to-the-graph"></a>將模組新增至圖形

1. 使用 Ctrl + F6，將焦點從畫布切換至模組樹狀結構。
1. 使用標準 treeview 控制項，在模組樹狀結構中尋找所需的模組。

### <a name="edit-a-module"></a>編輯模組

若要將模組連接到另一個模組：

1. 當以模組清單中的模組為目標時，請使用 Ctrl + Shift + H 來開啟連接協助程式。
1. 編輯模組的連線通訊埠。

若要調整模組屬性：

1. 當以模組為目標時，請使用 Ctrl + Shift + E 來開啟模組屬性。
1. 編輯模組屬性。

## <a name="navigation-shortcuts"></a>導覽快捷方式

| 按鍵輸入 | 說明 |
|-|-|
| Ctrl + F6 | 切換畫布與模組樹狀結構之間的焦點 |
| Ctrl + F1   | 將焦點放在模組樹狀結構中的節點時，開啟資訊卡片 |
| Ctrl + Shift + H | 當焦點在節點上時，開啟連接協助程式 |
| Ctrl + Shift + E | 當焦點在節點上時開啟模組屬性 |
| Ctrl + G | 如果管線執行失敗，將焦點移至第一個失敗的節點 |

## <a name="action-shortcuts"></a>動作快捷方式

使用下列快捷方式搭配存取金鑰。 如需存取金鑰的詳細資訊，請參閱 https://en.wikipedia.org/wiki/Access_key 。

| 按鍵輸入 | 行動 |
|-|-|
| 存取金鑰 + R | 執行 |
| 存取金鑰 + P | 發佈 |
| 存取金鑰 + C | 複製 |
| 存取金鑰 + D | 部署 |
| 存取金鑰 + I | 建立/更新推斷管線 |
| 存取金鑰 + B | 建立/更新批次推斷管線 |
| 存取金鑰 + K | 開啟 [建立推斷管線] 下拉式清單 |
| 存取金鑰 + U | 開啟 [更新推斷管線] 下拉式清單 |
| 存取金鑰 + M | [開啟更多（...）] 下拉式清單 |

## <a name="next-steps"></a>後續步驟

- [開啟高對比或變更佈景主題](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Microsoft 的協助工具相關工具](https://www.microsoft.com/accessibility)
