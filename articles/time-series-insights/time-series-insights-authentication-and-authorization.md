---
title: API 驗證和授權-Azure 時間序列深入解析 |Microsoft Docs
description: 本文說明如何為呼叫 Azure Time Series Insights API 的自訂應用程式設定驗證和授權。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: seodec18
ms.openlocfilehash: 960eb9b48a158358a076202db0d435feb918a6c8
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863473"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API 的驗證和授權

本檔說明如何使用新的 Azure Active Directory 分頁，在 Azure Active Directory 中註冊應用程式。 在 Azure Active Directory 中註冊的應用程式可讓使用者對與時間序列深入解析環境相關聯的 Azure 時間序列深入解析 API 進行驗證及授權使用。

> [!IMPORTANT]
> Azure 時間序列深入解析支援下列兩種驗證程式庫：
> * 最新的[Microsoft 驗證程式庫（MSAL）](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory Authentication Library （ADAL）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>服務主體

下列各節說明如何設定應用程式，以代表應用程式存取時間序列深入解析 API。 然後，應用程式可以透過 Azure Active Directory 使用自己的應用程式認證，在時間序列深入解析環境中查詢或發佈參考資料。

## <a name="summary-and-best-practices"></a>摘要和最佳作法

Azure Active Directory 應用程式註冊流程牽涉到三個主要步驟。

1. 在 Azure Active Directory 中[註冊應用程式](#azure-active-directory-app-registration)。
1. 授權應用程式擁有[時間序列深入解析環境的資料存取權](#granting-data-access)。
1. 使用**應用程式識別碼**和**用戶端密碼**，從[用戶端應用程式](#client-app-initialization)中的 `https://api.timeseries.azure.com/` 取得權杖。 此權杖可用來呼叫 Time Series Insights API。

根據**步驟 3**，將您的應用程式和使用者認證分開，可讓您：

* 將許可權指派給與您自己的許可權不同的應用程式身分識別。 一般而言，這些權限只會限制為 App 所需的權限。 例如，您可以允許應用程式只從特定的時間序列深入解析環境中讀取資料。
* 使用**用戶端密碼**或安全性憑證，將應用程式的安全性與建立使用者的驗證認證隔離。 因此，應用程式的認證不會相依于特定使用者的認證。 如果使用者的角色變更，應用程式不一定需要新的認證或進一步的設定。 如果使用者變更其密碼，對應用程式的所有存取都不需要新的認證或金鑰。
* 使用**用戶端密碼**或安全性憑證來執行自動腳本，而不是特定使用者的認證（需要有這些認證）。
* 使用安全性憑證而非密碼來保護 Azure 時間序列深入解析 API 的存取。

> [!IMPORTANT]
> 設定 Azure 時間序列深入解析安全性原則時，請遵循**關注點分離的**原則（針對上述案例說明）。

> [!NOTE]
> * 本文著重于單一租使用者應用程式，其中應用程式只會在一個組織中執行。
> * 您通常會將單一租使用者應用程式用於組織內執行的企業營運系統應用程式。

## <a name="detailed-setup"></a>詳細設定

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory 應用程式註冊

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>授與資料存取權

1. 針對時間序列深入解析環境，選取 [**資料存取原則**]，然後選取 [**新增**]。

   [![將新的資料存取原則新增至時間序列深入解析環境](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. 在 [**選取使用者**] 對話方塊中，貼上 [Azure Active Directory 應用程式註冊] 區段中的 [**應用程式名稱**] 或 [**應用程式識別碼**]。

   [在 [選取使用者] 對話方塊中 ![尋找應用程式](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. 選取角色。 選取 [**讀取器**] 來查詢資料或**參與者**，以查詢資料並變更參考資料。 選取 [確定]。

   [![選取 [選取使用者角色] 對話方塊中的 [讀取器] 或 [參與者]](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. 選取 **[確定]** 以儲存原則。

   > [!TIP]
   > 如需先進的資料存取選項，請參閱授與[資料存取權](./time-series-insights-data-access.md)。

### <a name="client-app-initialization"></a>用戶端應用程式初始化

* 開發人員可以使用[Microsoft 驗證程式庫（MSAL）](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)或[Azure Active Directory AUTHENTICATION library （ADAL）](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)來向 Azure 時間序列深入解析進行驗證。

* 例如，若要使用 ADAL 進行驗證：

   1. 使用 [Azure Active Directory 應用程式註冊] 區段中的 [**應用程式識別碼**] 和 [**用戶端密碼**] （應用程式金鑰），代表應用程式取得權杖。

   1. 在C#中，下列程式碼可以代表應用程式取得權杖。 如需完整範例，請參閱[使用C#來讀取查詢資料](time-series-insights-query-data-csharp.md)。

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. 當應用程式呼叫 Time Series Insights API 時，此權杖即可傳入 `Authorization` 標頭。

* 或者，開發人員可以選擇使用 MSAL 進行驗證。 如需深入瞭解，請參閱[遷移至 MSAL](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration) 。 

## <a name="common-headers-and-parameters"></a>一般標頭和參數

本節說明用來對時間序列深入解析 GA 和預覽 Api 進行查詢的一般 HTTP 要求標頭和參數。 [時間序列深入解析 REST API 參考檔](https://docs.microsoft.com/rest/api/time-series-insights/)中會更詳細地涵蓋 API 特定需求。

> [!TIP]
> 閱讀[Azure REST API 參考](https://docs.microsoft.com/rest/api/azure/)，以深入瞭解如何使用 REST api、提出 HTTP 要求，以及處理 HTTP 回應。

### <a name="authentication"></a>驗證

若要對[時間序列深入解析 REST api](https://docs.microsoft.com/rest/api/time-series-insights/)執行已驗證的查詢，必須使用您選擇的 REST 用戶端（Postman、JavaScript、 C#），在[授權標頭](/rest/api/apimanagement/2019-01-01/authorizationserver/createorupdate)中傳遞有效的 OAuth 2.0 持有人權杖。 

> [!TIP]
> 閱讀裝載的 Azure 時間序列深入解析[用戶端 sdk 範例視覺效果](https://tsiclientsample.azurewebsites.net/)，以瞭解如何使用[JAVASCRIPT 用戶端 SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)搭配圖表和圖形，以程式設計方式驗證時間序列深入解析 api。

### <a name="http-headers"></a>HTTP 標頭

必要的要求標頭如下所述。

| 必要的要求標頭 | 說明 |
| --- | --- |
| 授權 | 若要使用時間序列深入解析進行驗證，必須在**Authorization**標頭中傳遞有效的 OAuth 2.0 持有人權杖。 | 

> [!IMPORTANT]
> 權杖必須完全發行至 `https://api.timeseries.azure.com/` 資源（也稱為權杖的「物件」）。
> * 因此，您的[Postman](https://www.getpostman.com/) **AuthURL**將會是： `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/`
> * `https://api.timeseries.azure.com/` 有效，但 `https://api.timeseries.azure.com` 不是。

選擇性的要求標頭如下所述。

| 選擇性的要求標頭 | 說明 |
| --- | --- |
| Content-Type | 僅支援 `application/json`。 |
| x-ms-client-request-id | 用戶端要求識別碼。 服務會記錄此值。 允許服務跨服務追蹤作業。 |
| x-ms-用戶端-會話識別碼 | 用戶端會話識別碼。 服務會記錄此值。 允許服務追蹤跨服務的一組相關作業。 |
| x-ms-用戶端-應用程式名稱 | 產生此要求的應用程式名稱。 服務會記錄此值。 |

選擇性但建議的回應標頭如下所述。

| 回應標頭 | 說明 |
| --- | --- |
| Content-Type | 只支援 `application/json`。 |
| x-ms-request-id | 伺服器產生的要求識別碼。 可以用來與 Microsoft 聯繫以調查要求。 |
| x-ms-屬性-找不到-行為 | GA API 選擇性回應標頭。 可能的值為 `ThrowError` （預設）或 `UseNull`。 |

### <a name="http-parameters"></a>HTTP 參數

> [!TIP]
> 如需詳細資訊，請[參閱參考檔](https://docs.microsoft.com/rest/api/time-series-insights/)中的必要和選擇性查詢資訊。

必要的 URL 查詢字串參數取決於 API 版本。

| 發行 | 可能的 API 版本值 |
| --- |  --- |
| 正式運作 | `api-version=2016-12-12`|
| 預覽 | `api-version=2018-11-01-preview` |
| 預覽 | `api-version=2018-08-15-preview` |

選擇性的 URL 查詢字串參數包括設定 HTTP 要求執行時間的超時。

| 選擇性查詢參數 | 說明 | 版本 |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 要求執行的伺服器端超時。 僅適用于[取得環境事件](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)和[取得環境匯總](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)api。 Timeout 值應採用 ISO 8601 持續時間格式，例如 `"PT20S"`，且應在 `1-30 s`範圍內。 預設值為 `30 s`。 | 正式上市 |
| `storeType=<storeType>` | 針對已啟用暖存放區的預覽環境，可以在 `WarmStore` 或 `ColdStore`上執行查詢。 查詢中的這個參數會定義應執行查詢的存放區。 如果未定義，則會在冷存放區上執行查詢。 若要查詢暖存放區， **storeType**必須設定為 `WarmStore`。 如果未定義，則會對冷存放區執行查詢。 | 預覽 |

## <a name="next-steps"></a>後續步驟

- 如需呼叫 GA 時間序列深入解析 API 的範例程式碼，請參閱[使用C#來讀取查詢資料](./time-series-insights-query-data-csharp.md)。

- 如需預覽時間序列深入解析 API 程式碼範例，請參閱[使用C#來讀取查詢預覽資料](./time-series-insights-update-query-data-csharp.md)。

- 如需 API 參考資訊，請參閱[查詢 api 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)檔。

- 瞭解如何[建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。
