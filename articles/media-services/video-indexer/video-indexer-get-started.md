---
title: 註冊 Video Indexer 並上傳您的第一支影片 - Azure
titleSuffix: Azure Media Services
description: 了解如何使用 Video Indexer 入口網站，註冊及上傳您的第一支影片。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: juliako
ms.openlocfilehash: 3a5ddf5bd4614b68e97e7616173a3e0640007530
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941547"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>快速入門：如何註冊及上傳您的第一支影片

本入門教學課程示範如何登入 Video Indexer 網站，以及如何上傳您的第一支影片。

建立影片索引器帳戶時，您可以選擇免費試用帳戶 (您可取得特定的免費編製索引分鐘數) 或付費選項 (您不會受限於配額)。 使用免費試用時，影片索引器最多可為網站使用者提供 600 分鐘的免費編製索引，以及為 API 使用者提供 2400 分鐘的免費索引編製。 使用付費選項時，您建立的影片索引器帳戶會[連線到您的 Azure 訂用帳戶和 Azure 媒體服務帳戶](connect-to-azure.md)。 您需支付已編製索引的分鐘數，以及 Azure 媒體服務帳戶相關費用。 

## <a name="sign-up-for-video-indexer"></a>註冊 Video Indexer

若要開始使用 Video Indexer 進行開發，請瀏覽至 [Video Indexer](https://www.videoindexer.com) 網站並註冊。

## <a name="upload-a-video-using-the-video-indexer-website"></a>使用 Video Indexer 網站上傳影片

> [!NOTE]
> 影片名稱不得超過 80 個字元。

### <a name="supported-file-formats-for-video-indexer"></a>影片索引子支援的檔案格式

如需可與影片索引子搭配使用的檔案格式清單，請參閱[輸入容器/檔案格式](../latest/media-encoder-standard-formats.md#input-containerfile-formats)一文。

### <a name="upload-a-video"></a>上傳影片

1. 登入 [Video Indexer](https://www.videoindexer.ai/) 網站。
2. 若要上傳影片，請按 [上傳]  按鈕或連結。

    ![上傳](./media/video-indexer-get-started/video-indexer-upload.png)

    上傳您的影片後，Video Indexer 會開始編製索引及分析影片。

    ![已上傳](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Video Indexer 完成分析後，您會收到通知，內含您的影片連結以及在影片中找到的簡短描述。 例如：人員、主題、OCR。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[上傳影片並編製索引](upload-index-videos.md)。

上傳影片並編製索引後，您可以開始使用 [Video Indexer](video-indexer-view-edit.md) 網站或 [Video Indexer 開發人員入口網站](video-indexer-use-apis.md)來查看影片的深入解析。 

## <a name="see-also"></a>另請參閱

[影片索引子概觀](video-indexer-overview.md)

[開始使用 API](video-indexer-use-apis.md)。

