---
title: 在 Azure IoT Central 應用程式中建立及執行工作 | Microsoft Docs
description: Azure IoT Central 工作可讓您執行大量裝置管理功能，例如更新裝置屬性、設定或執行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 114946fa37ae161aeb2efd5b7cd50444c5df4c2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906703"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在您的 Azure IoT Central 應用程式中建立及執行作業

您可以使用 Microsoft Azure IoT Central 利用工作來大規模管理您的已連線裝置。 作業可讓您對裝置屬性和命令進行大量更新。 本文會逐步引導您瞭解如何開始在自己的應用程式中使用作業。


## <a name="create-and-run-a-job"></a>建立及執行工作

本節說明如何建立及執行工作。 其中說明如何增加多部冷飲自動販賣機器的風扇速度。

1. 從瀏覽窗格中瀏覽至 [工作]。

2. 選取 [ **+ 新增**] 以建立新的作業。

    ![建立新工作](./media/howto-run-a-job/createnewjob.png)

3. 輸入 [名稱] 和 [描述] 來識別您要建立的作業。

4. 選取您要套用作業的裝置群組。 您可以在 [摘要] 區段中，查看您的作業設定將套用至多少裝置。 

5. 接下來，選擇要定義的作業類型（屬性或命令）。 選取屬性並設定新的值，或選擇命令，以設定作業設定。 您可以一次新增多個屬性。

    ![設定工作](./media/howto-run-a-job/configurejob.png)

6. 選取您的裝置之後，請選擇 [**執行**] 或 [**儲存**]。 作業現在會出現在您的主要 [**作業**] 頁面上。 在此視圖中，您可以看到目前正在執行的作業，以及任何先前執行之作業的歷程記錄。 執行中的作業一律會顯示在清單頂端。 您已儲存的作業可以隨時重新開啟，以繼續編輯或執行。

    ![檢視工作](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 您可以查看先前執行之工作的歷程記錄（最多30天）。

7. 若要取得作業的總覽，請從清單中選取要查看的作業。 此總覽包含作業詳細資料、裝置和裝置狀態值。 在此總覽中，您也可以選取 [**下載工作詳細資料**] 來下載作業詳細資料的 .csv 檔案，包括裝置及其狀態值。 這種資訊對疑難排解很有用。

    ![檢視裝置狀態](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>停止執行中的工作

若要停止正在執行的作業，請選取它，然後選擇 [**停止**]。 作業狀態會變更以反映作業已停止。

   ![停止作業](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>執行已停止的作業

若要執行目前已停止的作業，請選取 [已停止] 作業。 選擇面板上的 [**執行**]。 作業狀態會變更，以反映作業現在正在執行。

   ![繼續的作業](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>複製作業

若要複製已建立的現有作業，請開啟已建立的作業，然後選取 [**複製**]。 隨即會開啟 [作業設定] 的新複本以供您編輯。 您可以儲存或執行新作業。 

   ![複製作業](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>檢視工作狀態

建立作業之後，[**狀態**] 資料行會以作業的最新狀態訊息更新。 下表列出可能的狀態值：

| 狀態訊息       | 狀態意義                                          |
| -------------------- | ------------------------------------------------------- |
| Completed            | 此工作已在所有裝置上執行。              |
| 失敗               | 此工作失敗而未在裝置上完整執行。  |
| Pending              | 此作業尚未開始在裝置上執行。         |
| 執行中              | 此工作目前正在裝置上執行。             |
| 已停止              | 此工作已被使用者手動停止。           |

狀態訊息後面會有作業中的裝置總覽。 下表列出可能的裝置狀態值：

| 狀態訊息       | 狀態意義                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 作業執行成功的裝置數目。       |
| 失敗               | 作業執行失敗的裝置數目。       |

### <a name="view-the-device-status"></a>檢視裝置狀態

若要查看作業和所有受影響裝置的狀態，請選取該作業。 若要下載包含作業詳細資料的 .csv 檔案，包括裝置清單及其狀態值，請選取 [**下載作業詳細資料**]。 在每個裝置名稱旁邊，您會看到下列其中一則狀態訊息：

| 狀態訊息       | 狀態意義                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Completed            | 工作已在此裝置上執行。                                     |
| 失敗               | 工作在此裝置上執行失敗。 錯誤訊息會顯示詳細資訊。  |
| Pending              | 作業尚未在此裝置上執行。                                   |

> [!NOTE]
> 如果裝置已被刪除，您就無法選取該裝置，它會以裝置識別碼顯示為已刪除。

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中建立作業，以下是一些後續步驟：

- [使用裝置集合](howto-use-device-sets.md)
- [管理您的裝置](howto-manage-devices.md)
- [建立裝置範本版本](howto-version-device-template.md)
