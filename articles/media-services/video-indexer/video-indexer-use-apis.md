---
title: 使用影片索引器 API
titleSuffix: Azure Media Services
description: 本文說明如何開始使用 Azure 媒體服務影片索引子 API。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 4da437fecf0529194d72b2d403302ff81055ec0f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892680"
---
# <a name="tutorial-use-the-video-indexer-api"></a>教學課程：使用影片索引子 API

影片索引子將 Microsoft 提供的各種音訊和影片人工智慧（AI）技術合併成一個整合式服務，讓開發變得更簡單。 這些 Api 的設計可讓開發人員專注于使用媒體 AI 技術，而不必擔心雲端平臺的規模、全球範圍、可用性和可靠性。 您可以使用 API 來上傳檔案、取得詳細的影片深入解析、取得可內嵌深入解析和播放程式 widget 的 Url 等等。

建立影片索引子帳戶時，您可以選擇免費試用帳戶（您可取得特定的免費編制索引分鐘數）或付費選項（您不會受限於配額）。 使用免費試用版，影片索引子最多可為網站使用者提供600分鐘的免費編制索引，以及最多2400分鐘的 API 使用者免費編制索引。 使用付費選項時，您會建立[連接到您的 Azure 訂用帳戶](connect-to-azure.md)的影片索引子帳戶，以及 Azure 媒體服務帳戶。 您需支付已編製索引的分鐘數，以及 Azure 媒體服務帳戶相關費用。

本文說明開發人員可以如何善用[影片索引器 API](https://api-portal.videoindexer.ai/)。

## <a name="subscribe-to-the-api"></a>訂閱 API

1. 登入[影片索引器開發人員入口網站](https://api-portal.videoindexer.ai/)。
    
    ![登入影片索引子開發人員入口網站](./media/video-indexer-use-apis/video-indexer-api01.png)

   > [!Important]
   > * 您必須使用註冊影片索引器時所使用的提供者。
   > * 個人 Google 和 Microsoft （Outlook/Live）帳戶只能用於試用帳戶。 連線至 Azure 的帳戶需要 Azure AD。
   > * 每封電子郵件只能有一個使用中的帳戶。 如果使用者嘗試使用適用于 Google 的 user@gmail.com user@gmail.com 的 LinkedIn 和更新版本進行登入，則後者會顯示錯誤頁面，指出使用者已存在。

2. 訂閱。

    選取 [[產品](https://api-portal.videoindexer.ai/products)] 索引標籤。然後，選取 [授權和訂閱]。
    
    ![影片索引子開發人員入口網站中的 [產品] 標籤](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > 系統會自動完成新使用者的授權訂閱。
    
    訂閱之後，您就可以看到您的訂用帳戶與主要和次要金鑰。 應妥善保護金鑰。 這些金鑰應只供伺服器程式碼使用。 用戶端不應該提供它們（.js、.html 等等）。

    ![影片索引子開發人員入口網站中的訂用帳戶和金鑰](./media/video-indexer-use-apis/video-indexer-api03.png)

> [!TIP]
> 影片索引器使用者可以使用單一訂用帳戶金鑰來連線到多個影片索引器帳戶。 接著，您可以將這些影片索引器帳戶連結到不同媒體服務帳戶。

## <a name="obtain-access-token-using-the-authorization-api"></a>使用授權 API 取得存取權杖

訂閱授權 API 後，您就可以取得存取權杖。 這些存取權杖可用來對作業 API 進行驗證。

作業 API 的每次呼叫皆應與存取權杖相關聯，以符合呼叫的授權範圍。

- 使用者層級：使用者層級的存取權杖可讓您在**使用者**層級上執行作業。 例如，取得相關聯的帳戶。
- 帳戶層級：帳戶層級的存取權杖可讓您在**帳戶**層級或**影片**層級上執行作業。 例如，上傳影片、列出所有影片、取得影片深入解析等等。
- 影片層級：影片層級的存取權杖可讓您在特定**影片**上執行作業。 例如，取得影片深入解析、下載字幕、取得 widget 等等。

您可以藉由指定**allowEdit = true/false**來控制這些權杖是否為唯讀或允許編輯。

對於大部分的伺服器對伺服器案例，您可能會使用相同的**帳戶**權杖，因為它涵蓋**帳戶**作業和**影片**作業。 不過，如果您打算對影片索引子進行用戶端呼叫（例如，從 JavaScript），您會想要使用**影片**存取權杖，以防止用戶端取得整個帳戶的存取權。 這也是在用戶端內嵌影片索引子用戶端程式代碼（例如，使用 [**取得深入解析 widget** ] 或 [**取得播放機] widget**）時的原因，您必須提供**影片**存取權杖。

為了方便起見，您可以使用**授權** API > **GetAccounts** 來取得您的帳戶，而不需先取得使用者權杖。 您也可以透過有效權杖來要求取得帳戶，以略過用於取得帳戶權杖的額外呼叫。

存取權杖會在 1 小時後過期。 請先確定您的存取權杖有效，再使用作業 API。 如果過期，請再次呼叫授權 API 以取得新的存取權杖。

您已經準備好開始與 API 整合。 請探索[每個影片索引器 REST API 的詳細描述](https://api-portal.videoindexer.ai/)。

## <a name="account-id"></a>帳戶識別碼

帳戶識別碼參數是所有作業 API 呼叫的必要項目。 帳戶識別碼是 GUID，可使用下列其中一個方法取得：

* 使用**影片索引器網站**來取得帳戶識別碼：

    1. 瀏覽至[影片索引子](https://www.videoindexer.ai/)網站並登入。
    2. 瀏覽至 [設定] 頁面。
    3. 複製帳戶識別碼。

        ![影片索引子設定和帳戶識別碼](./media/video-indexer-use-apis/account-id.png)

* 使用**影片索引器開發人員入口網站**，以程式設計方式取得帳戶識別碼。

    使用[取得帳戶](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API。

    > [!TIP]
    > 您可以藉由定義 `generateAccessTokens=true` 來產生帳戶的存取權杖。

* 從帳戶中的播放程式頁面 URL 取得帳戶識別碼。

    當您觀看影片時，識別碼會出現在 `accounts` 區段之後及 `videos` 區段之前。

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>建議

本節會列出使用影片索引器 API 時的一些建議。

- 如果您打算上傳影片，建議您將檔案放在某個公用網路位置（例如 OneDrive）。 取得影片的連結，並將此 URL 作為上傳檔案參數。

    提供給影片索引器的 URL 必須指向媒體 (音訊或視訊) 檔案。 有些 OneDrive 所產生的連結是包含檔案的 HTML 網頁。 URL 的簡單驗證是將它貼到瀏覽器中-如果檔案開始下載，可能是很好的 URL。 如果瀏覽器正在轉譯某些視覺效果，則可能不是檔案的連結，而是 HTML 網頁的連結。

- 當您呼叫 API 以取得指定影片的影片深入解析時，取得的回應內容會是詳細的 JSON 輸出。 [請在本主題中參閱傳回的 JSON 詳細資料](video-indexer-output-json-v2.md)。

## <a name="code-sample"></a>程式碼範例

下列 C# 程式碼片段會示範一起使用所有影片索引器 API 的方式。

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="see-also"></a>請參閱

- [影片索引子概觀](video-indexer-overview.md)
- [區域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>後續步驟

[查看輸出 JSON 的詳細資料](video-indexer-output-json-v2.md)。
