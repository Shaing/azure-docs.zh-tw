---
title: 將 DevKit 裝置連線到 Azure IoT 中心應用程式 | Microsoft Docs
description: 身為裝置開發人員，請瞭解如何使用 IoT 隨插即用將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式。
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: 9ba68e90b5ac64c7bdb08af2b567f00c1a076808
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453926"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application-preview-features"></a>將 MXChip IoT DevKit 裝置連線到您的 Azure IoT Central 應用程式（預覽功能）

本文說明如何將 MXChip IoT DevKit （DevKit）裝置連線到 Azure IoT Central 應用程式。 裝置會使用適用于 DevKit 裝置的認證 IoT 隨插即用模型，設定其與 IoT Central 的連接。

在此操作說明文章中，您會：

- 從您的 IoT Central 應用程式取得連接詳細資料。
- 準備裝置，並將它連線到您的 IoT Central 應用程式。
- 在 IoT Central 中，從裝置中查看遙測和屬性。

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您需要下列資源：

- [DevKit 裝置](https://aka.ms/iot-devkit-purchase)。
- 從**預覽應用程式**範本建立的 IoT Central 應用程式。 您可以依照[建立 IoT 隨插即用應用程式](./quick-deploy-iot-central.md)中的步驟進行。

## <a name="get-device-connection-details"></a>取得裝置連線詳細資料

1. 在 Azure IoT Central 應用程式中，選取 [**裝置範本**] 索引標籤，然後選取 [**新增**]。 在 [**使用預先設定的裝置範本**] 的區段中，從清單中選擇 [ **MXChip IoT DevKit** ]。 然後選擇 **[下一步]： [自訂**並**建立**]。

    ![適用于 MXChip IoT DevKit 的裝置範本](media/howto-connect-devkit/device-template.png)

1. 選取 [**裝置**] 索引標籤，在 [裝置] 清單中選取 [ **MXChip IoT DevKit** ]，然後選取 [**新增**] 以從裝置範本建立新裝置。

    ![新增裝置](media/howto-connect-devkit/new-device.png)

1. 在快顯視窗中，將 [**裝置識別碼**] 輸入為 `SampleDevKit` 和 [**裝置名稱**] 做為 [`MXChip IoT DevKit - Sample`]。 確定已關閉**模擬**選項。 然後選取 [建立]。

    ![裝置識別碼和名稱](media/howto-connect-devkit/device-id-name.png)

1. 按一下您剛建立的裝置，然後選取 **[連線]** 。 記下 [**識別碼範圍**]、[**裝置識別碼**] 和 [**主要金鑰**]。

    ![裝置連接資訊](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>準備裝置

1. 從 GitHub 下載適用于 DevKit 裝置的最新[預先建立的 Azure IoT Central 隨插即用固件](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin)。

1. 使用 USB 纜線將 DevKit 裝置連接到開發電腦。 在 Windows 中，檔案總管視窗會在對應到 DevKit 裝置上儲存體的磁碟機上開啟。 例如，此磁碟機可能會稱為 **AZ3166 (D:)** 。

1. 將 [ **iotc_devkit] bin**檔案拖曳至 [磁片磁碟機] 視窗。 複製完成時，使用新的韌體重新啟動裝置。

    > [!NOTE]
    > 如果您在畫面上看到錯誤，例如**沒有 wi-fi**，這是因為 DevKit 尚未連線到 WiFi。

1. 在 [DevKit] 上，按住**按鈕 b**，按下並放開 [**重設**] 按鈕，然後放開**按鈕 B**。裝置現在處於「存取點」模式。 若要確認，此畫面會顯示「IoT DevKit-AP」和設定入口網站的 IP 位址。

1. 在您的電腦或平板電腦上，連接到裝置螢幕上顯示的 WiFi 網路名稱。 WiFi 網路的開頭為**AZ-** 後面接著 MAC 位址。 當您連接到此網路時，您無法存取網際網路。 這是預期的狀態，而且您在設定裝置時，只會短時間連線到此網路。

1. 開啟網頁瀏覽器並巡覽至 [http://192.168.0.1/](http://192.168.0.1/)。 以下網頁隨即顯示：

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    在網頁上，輸入：

    - 您的 WiFi 網路（SSID）的名稱。
    - 您的 WiFi 網路密碼。
    - 連線詳細資料：輸入您先前所記下的 [**裝置識別碼**]、[**識別碼範圍**] 和 [ **SAS 主要金鑰**]。

    > [!NOTE]
    > 目前，IoT DevKit 只能連線到 2.4 GHz 的 Wi-fi，由於硬體限制，不支援 5 GHz。

1. 選擇 [**設定裝置**]，DevKit 裝置會重新開機並執行應用程式：

    ![重新開機 UI](media/howto-connect-devkit/reboot-ui.png)

    [DevKit] 畫面會顯示應用程式正在執行的確認：

    ![DevKit 執行中](media/howto-connect-devkit/devkit-running.png)

DevKit 會先在 IoT Central 應用程式中註冊新裝置，然後開始傳送資料。

## <a name="view-the-telemetry"></a>檢視遙測資料

在此步驟中，您會在 Azure IoT Central 應用程式中查看遙測。

在您的 IoT Central 應用程式中，選取 [**裝置**] 索引標籤，選取您新增的裝置。 在 [**總覽**] 索引標籤中，您可以看到來自 DevKit 裝置的遙測：

![IoT Central 裝置總覽](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>檢閱程式碼

若要檢查程式碼或修改並編譯它，請移至程式[代碼範例](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何將 DevKit 裝置連線到您的 Azure IoT Central 應用程式，建議的下一個步驟是瞭解如何為您自己的 IoT 裝置[設定自訂裝置範本](./howto-set-up-template.md)。
