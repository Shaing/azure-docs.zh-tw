---
title: 了解 Azure IoT 中心應用程式的裝置範本版本設定 | Microsoft Docs
description: 藉由建立新版本來反覆使用裝置範本，而不會影響您的即時連線裝置
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1f2ecb37ebe156b1eb092bda95f296c39c9e2baf
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974959"
---
# <a name="create-a-new-device-template-version-preview-features"></a>建立新的裝置範本版本（預覽功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Azure IoT Central 可讓您快速開發 IoT 應用程式。 您可以藉由新增、編輯或刪除裝置功能、視圖和自訂，來快速反復查看裝置範本設計。 當您發佈裝置範本之後，裝置功能模型會顯示為 [**已發佈**]，其中包含模型旁的鎖定圖示。 若要對裝置功能模型進行變更，您必須建立新版本的裝置範本。 同時，您可以隨時編輯雲端屬性、自訂和 views，而不需要為裝置範本版本。 儲存任何變更之後，您可以發佈裝置範本，讓操作員可以在 Device Explorer 中看到最新的變更。

> [!NOTE]
> 若要深入瞭解如何建立裝置範本，請參閱[設定和管理裝置範本](howto-set-up-template.md)

## <a name="add-customizations-to-the-device-template-without-versioning"></a>將自訂新增至裝置範本而不進行版本設定

您可以編輯裝置功能的某些元素，而不需要為您的裝置範本和介面進行版本。 例如，其中一些欄位包括顯示名稱、語義類型、最小值、最大值、小數位數、色彩、單位、顯示單位、批註和描述。 若要加入其中一個自訂專案：

1. 移至 [**裝置範本**] 頁面。
1. 選取您想要自訂的裝置範本。
1. 選擇 [**自訂**] 索引標籤。
1. 您的裝置功能模型中所定義的所有功能都將列在此處。 您可以在這裡編輯的所有欄位都可以儲存並用於您的應用程式，而不需要為您的裝置範本版本。 如果您想要編輯的欄位是唯讀的，您必須將裝置範本的版本設為 [變更]。 選取您想要編輯的欄位，並輸入任何新的值。
1. 按一下 [儲存]。 現在，這些值將會覆寫您的裝置範本中一開始儲存的任何內容，而且將會在整個應用程式中使用。

## <a name="versioning-a-device-template"></a>版本設定裝置範本

建立新版本的裝置範本會建立範本的草稿版本，可在其中編輯裝置功能模型。 任何已發行的介面都將保持發行，直到個別設定版本。 為了修改已發佈的介面，您必須先建立新的裝置範本版本。

當您嘗試在裝置範本的 [自訂] 區段中編輯無法編輯的部分裝置功能模型時，裝置範本應該只會建立版本。 

若要將裝置範本設為版本：

1. 移至 [**裝置範本**] 頁面。
1. 選取您嘗試進行版本的裝置範本。
1. 按一下頁面頂端的 [**版本**] 按鈕，並為範本指定新的名稱。 我們為您建議了可編輯的新名稱。
1. 按一下 [建立]。
1. 現在您的裝置範本處於草稿模式。 您會看到您的介面仍處於鎖定狀態，而且必須個別設定版本才能進行編輯。 

### <a name="versioning-an-interface"></a>版本設定介面

設定介面版本可讓您在已建立的介面內新增、更新和移除功能。 

若要將介面設為版本：

1. 移至 [**裝置範本**] 頁面。
1. 選取您在草稿模式中的裝置範本。
1. 選取您想要進行版本與編輯之已發行模式中的介面。
1. 按一下 [介面] 頁面頂端的 [**版本**] 按鈕。 
1. 按一下 [建立]。
1. 現在您的介面處於草稿模式。 您將能夠在介面中新增或編輯功能，而不會中斷現有的自訂和視圖。 

> [!NOTE]
> Azure IoT 所發佈的標準介面無法進行版本設定或編輯。 這些標準介面是用於裝置認證。

> [!NOTE]
> 一旦介面發佈之後，您就無法刪除其中任何一項功能，即使是在草稿模式中也一樣。 功能只能在草稿模式中編輯或新增至介面。


## <a name="migrate-a-device-across-device-template-versions"></a>跨裝置範本版本移轉裝置

您可以建立多個版本的裝置範本。 經過一段時間，您會有使用這些裝置範本的多個連線裝置。 您可以將裝置從一個版本的裝置範本移轉至另一個版本。 下列步驟說明如何移轉裝置：

1. 移至 [ **Device Explorer** ] 頁面。
1. 選取您需要移轉到另一個版本的裝置。
1. 選擇 [**遷移**]。
1. 選取您想要將裝置遷移至哪個版本號碼的裝置範本，然後選擇 [**遷移**]。

![如何移轉裝置](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>後續步驟

您現在已了解如何在 Azure IoT 中心應用程式中使用裝置範本版本，以下是建議後續的步驟：

> [!div class="nextstepaction"]
> [如何建立遙測規則](tutorial-create-telemetry-rules.md)
