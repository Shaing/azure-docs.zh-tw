---
title: 如何在 Azure API 管理中建立及發行產品
description: 了解如何在 Azure API 管理中建立及發行產品。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 813117bce4abd3550ac559109c1650ea4a7cff5c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538253"
---
# <a name="create-and-publish-a-product"></a>建立和發行產品  

在 Azure API 管理中，產品包含一或多個 API，以及使用量配額與使用規定。 發行產品之後，開發人員便可訂閱產品，並開始使用產品的 API。  

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

![新增產品教學課程](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>必要條件

+ 了解 [Azure API 管理術語](api-management-terminology.md)。
+ 完成下列快速入門：[建立 Azure APIM 執行個體](get-started-create-service-instance.md)。
+ 此外，請完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

## <a name="create-and-publish-a-product"></a>建立和發行產品

![Add product](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. 按一下左邊功能表中的 [產品]  ，以顯示 [產品]  頁面。
2. 按一下 [+ 新增]  。

    當您新增產品時，您需要提供下列資訊： 

    | 名稱                     | 說明                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 顯示名稱             | 您想要其顯示在**開發人員入口網站**中的名稱。                                                                                                                                                                                                                                                        |
    | 名稱                     | 產品的描述性名稱。                                                                                                                                                                                                                                                                                      |
    | 說明              | [說明]  欄位可讓您提供產品的詳細資訊，例如用途、產品可供其存取的 API 及其他有用的資訊。                                                                                                                                               |
    | State                    | 如果您想要發行產品，請按 [發行]  。 產品必須發行，才能呼叫產品中的 API。 依預設不會發行新產品，且只有 [Administrators]  群組才能看見。                                                                                      |
    | 需要訂用帳戶    | 如果使用者必須先訂閱才可使用產品，請核取 [需要訂用帳戶]  。                                                                                                                                                                                                                                   |
    | 需要核准        | 如果您希望管理員檢閱並接受或拒絕對此產品的訂閱嘗試，請核取 [需要核准]  。 如果未核取方塊，將會自動核准訂閱嘗試。                                                                                                                         |
    | 訂閱計數限制 | 若要限制多個訂閱同時進行的計數，請輸入訂閱限制。                                                                                                                                                                                                                                |
    | 法律條款              | 您可以包含訂閱者必須接受才可使用產品的產品使用規定。                                                                                                                                                                                                             |
    | API                     | 產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 <br/> 在產品建立期間，您可以新增現有的 API。 您之後可以將 API 新增至產品，從產品的 [設定]  頁面或在建立 API 時進行新增。 |

3. 按一下 [建立]  來建立新產品。

### <a name="add-more-configurations"></a>新增更多設定

將其儲存之後，您可以選擇 [設定]  索引標籤，繼續設定產品。 

從 [訂用帳戶]  索引標籤中，檢視/新增產品訂閱者。

從 [存取控制]  索引標籤中，設定開發人員或來賓適用的產品可見性。

## <a name="add-apis"> </a>將 API 加入至產品

產品是一或多個 API 的關聯。 您可以包括數個 API，並透過開發人員入口網站將它們提供給開發人員。 在產品建立期間，您可以新增現有的 API。 您可以在之後將 API 新增至產品中，可以從產品的 [設定]  頁面或建立 API 時進行新增。

開發人員必須先訂閱產品，才能取得 API 的存取權。 當他們訂閱時，就能取得適用於該產品中任何 API 的中訂用帳戶金鑰。 如果您建立了 APIM 執行個體，您就已經是系統管理員，因此根據預設，您已訂閱每一項產品。

### <a name="add-an-api-to-an-existing-product"></a>將 API 新增至現有的產品

![新增產品 API](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. 在 [產品]  索引標籤上選取產品。
2. 瀏覽至 [API]  索引標籤。
3. 按一下 [+ 新增]  。
4. 選擇 API，然後按一下 [選取]  。

> [!TIP]
> 您可以[透過 REST API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate) 或 PowerShell 命令，使用自訂的訂用帳戶金鑰建立或更新使用者的*產品*訂用帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立和發行產品
> * 將 API 新增至產品

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [建立空白 API 和模擬 API 回應](mock-api-responses.md)
