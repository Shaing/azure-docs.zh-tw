---
title: 在管線活動中使用 Azure Key Vault 祕密
description: 瞭解如何從 Azure key vault 提取預存認證，並在 data factory 管線執行期間加以使用。
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: 837d62784a56ad0f17471cca5a660819d4a83e12
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926766"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>在管線活動中使用 Azure Key Vault 祕密

您可以在 Azure Key Vault 中儲存認證或秘密值，並在管線執行期間將其用於傳遞至您的活動。

## <a name="prerequisites"></a>必要條件

此功能依賴 data factory 受控識別。  瞭解[Data Factory 的受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)如何運作，並確定您的 Data Factory 有一個相關聯的。

## <a name="steps"></a>步驟

1. 開啟 data factory 的屬性，並複製 [受控識別應用程式識別碼] 值。

    ![受控識別值](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 開啟金鑰保存庫存取原則，並新增「受控識別」許可權以取得及列出秘密。

    ![Key Vault 存取原則](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 存取原則](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    按一下 [**新增**]，然後按一下 [**儲存**]。

3. 流覽至您的 Key Vault 密碼，並複製秘密識別碼。

    ![報表識別碼](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    記下您想要在 data factory 管線執行期間取得的秘密 URI。

4. 在您的 Data Factory 管線中，新增新的 Web 活動並加以設定，如下所示。  

    |屬性  |Value  |
    |---------|---------|
    |保護輸出     |是         |
    |URL     |[您的秘密 URI 值]？ api-版本 = 7。0         |
    |方法     |GET         |
    |Authentication     |MSI         |
    |資源        |https://vault.azure.net       |

    ![Web 活動](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 您必須將 **？ api 版本 = 7.0**新增至您的秘密 URI 結尾。  

    > [!CAUTION]
    > 將 [安全輸出] 選項設定為 [true]，以防止以純文字記錄秘密值。  任何進一步使用此值的活動都應該將其 Secure 輸入選項設定為 true。

5. 若要使用另一個活動中的值，請使用下列程式碼運算式 **@activity（"web"）。輸出值）** 。

    ![程式碼運算式](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何使用 Azure Key Vault 來儲存資料存放區和計算的認證，請參閱[將認證儲存在 Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
