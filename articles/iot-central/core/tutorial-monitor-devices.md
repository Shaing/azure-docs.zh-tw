---
title: 教學課程 - 在 Azure IoT Central 中監視您的裝置
description: 本教學課程說明操作員如何使用 Azure IoT Central 應用程式監視裝置。
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c04793f22e146491efdffc81f28e1719542af054
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705850"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>教學課程：使用 Azure IoT Central 監視您的裝置

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本教學課程將為操作員說明如何使用 Microsoft Azure IoT Central 應用程式監視您的裝置和變更設定。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 接收通知
> * 調查問題
> * 補救問題

## <a name="prerequisites"></a>必要條件

在開始之前，建置者應先完成三個建置者教學課程，以建立 Azure IoT Central 應用程式：

* [定義新的裝置類型](tutorial-define-device-type.md)
* [為您的裝置設定規則和動作](tutorial-configure-rules.md)
* [自訂操作員的檢視](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>接收通知

Azure IoT Central 會以電子郵件訊息傳送關於裝置的通知。 建置者已新增會在連線的空調裝置溫度超出閾值時傳送通知的規則。 請查看傳送至建置者選擇要接收通知之帳戶的電子郵件。

開啟您在[為您的裝置設定規則和動作](tutorial-configure-rules.md)教學課程末尾收到的電子郵件訊息。 在電子郵件的 [詳細資料]  區段中，選取 [裝置名稱]  旁的裝置連結：

![警示通知電子郵件](media/tutorial-monitor-devices/email.png)

您在先前的教學課程中建立的**連線的空調-1** 模擬裝置的 [裝置]  頁面，會在您的瀏覽器中開啟：

![觸發通知電子郵件訊息的裝置](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>調查問題

身為操作員，您可以在 [測量]  、[設定]  、[屬性]  、[規則]  和 [儀表板]  頁面上檢視裝置的相關資訊。 建置者自訂了 [儀表板]  ，以顯示與連線的空調裝置有關的重要資訊。

選擇 [儀表板]  檢視以查看裝置的相關資訊。

![裝置儀表板](media/tutorial-monitor-devices/initial_screen.png)

儀表板上的圖表會顯示裝置溫度的繪圖。 您也可以在 [裝置屬性]  圖格中檢視裝置目前的目標溫度。 您判定目標溫度過高。

## <a name="remediate-an-issue"></a>補救問題

若要變更裝置的目標溫度，請使用 [設定]  頁面：

1. 選擇 [設定]  。 將 [設定溫度]  變更為 75。 選擇 [更新]  ，以將新的目標溫度傳送至裝置。 當裝置確認設定變更之後，設定的狀態將會變更為 [已同步]  ：

    ![更新設定](media/tutorial-monitor-devices/change_settings.png)

2. 選擇 [儀表板]  ，並確認新的設定值：

    ![已更新的裝置儀表板](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="nextstepaction"]
> * 接收通知
> * 調查問題
> * 補救問題

現在，您已了解如何監視您的裝置，建議的下一個步驟是[新增裝置](tutorial-add-device.md)。
