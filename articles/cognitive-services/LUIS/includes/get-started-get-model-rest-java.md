---
title: 在 Java 中使用 REST 呼叫取得模型
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/18/2019
ms.author: diberry
ms.openlocfilehash: a8129bf057b0e80a5f656f5ab3d578156b18a23c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500716"
---
## <a name="prerequisites"></a>必要條件

* 入門金鑰。
* 匯入來自 cognitive-services-language-understanding GitHub 存放庫的 [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) 應用程式。
* 已匯入 TravelAgent 應用程式的 LUIS 應用程式識別碼。 應用程式識別碼會顯示在應用程式儀表板中。
* 應用程式中用來接收表達的版本識別碼。 預設識別碼為 "0.1"。
* [JDK SE](https://aka.ms/azure-jdks) (英文) (Java 開發套件，標準版)
* [Visual Studio Code](https://code.visualstudio.com/) 或您最愛的 IDE

## <a name="example-utterances-json-file"></a>範例語句 JSON 檔案

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="get-luis-key"></a>取得 LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/get-key-quickstart.md)]

## <a name="change-model-programmatically"></a>以程式設計方式變更模型

使用 GO 將機器學習的實體 [API](https://aka.ms/luis-apim-v3-authoring) 新增至應用程式。 

1. 建立名為 `lib` 的子目錄，並在下列的 java 程式庫中複製：

    * [commons-logging-1.2.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/commons-logging-1.2.jar)
    * [httpclient-4.5.3.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpclient-4.5.3.jar)
    * [httpcore-4.4.6.jar](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/quickstarts/analyze-text/java/lib/httpcore-4.4.6.jar)

1. 建立名為 `Model.java` 的新檔案。 新增下列程式碼：


    ```java
    import java.io.*;
    import java.net.URI;
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.HttpClient;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    //javac -cp ":lib/*" Model.java
    //java -cp ":lib/*" Model
    
    public class Model {
    
        public static void main(String[] args) 
        {
            try
            {
    
                // The ID of a public sample LUIS app that recognizes intents for turning on and off lights
                String AppId = "YOUR-APP-ID";
                
                // Add your endpoint key 
                String Key = "YOUR-KEY";
    
                // Add your endpoint, example is westus.api.cognitive.microsoft.com
                String Endpoint = "YOUR-ENDPOINT";
    
                String Utterance = "[{'text': 'go to Seattle today','intentName': 'BookFlight','entityLabels': [{'entityName': 'Location::LocationTo',"
                    + "'startCharIndex': 6,'endCharIndex': 12}]},{'text': 'a barking dog is annoying','intentName': 'None','entityLabels': []}]";
    
                String Version = "1.0";
    
                // Begin endpoint URL string building
                URIBuilder addUtteranceURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/examples");
                URIBuilder trainURL = new URIBuilder("https://" + Endpoint + "/luis/authoring/v3.0-preview/apps/" + AppId + "/versions/" + Version + "/train");
    
                // create URL from string
                URI addUtterancesURI = addUtteranceURL.build();
                URI trainURI = trainURL.build();
    
                // add utterances POST
                HttpClient addUtterancesClient = HttpClients.createDefault();
                HttpPost addutterancesRequest = new HttpPost(addUtterancesURI);
                addutterancesRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                addutterancesRequest.setHeader("Content-type","application/json");
                HttpResponse addutterancesResponse = addUtterancesClient.execute(addutterancesRequest);
                HttpEntity addutterancesEntity = addutterancesResponse.getEntity();
                if (addutterancesEntity != null) 
                {
                    System.out.println(EntityUtils.toString(addutterancesEntity));
                }
    
                // train POST
                HttpClient trainClient = HttpClients.createDefault();
                HttpPost trainRequest = new HttpPost(trainURI);
                trainRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainRequest.setHeader("Content-type","application/json");
                HttpResponse trainResponse = trainClient.execute(trainRequest);
                HttpEntity trainEntity = trainResponse.getEntity();
                if (trainEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainEntity));
                }
    
                // training status GET
                HttpClient trainStatusClient = HttpClients.createDefault();
                HttpGet trainStatusRequest = new HttpGet(trainURI);
                trainStatusRequest.setHeader("Ocp-Apim-Subscription-Key",Key);
                trainStatusRequest.setHeader("Content-type","application/json");
                HttpResponse trainStatusResponse = trainStatusClient.execute(trainStatusRequest);
                HttpEntity trainStatusEntity = trainStatusResponse.getEntity();
                if (trainStatusEntity != null) 
                {
                    System.out.println(EntityUtils.toString(trainStatusEntity));
                }            
            }
    
            catch (Exception e)
            {
                System.out.println(e.getMessage());
            }
        }   
    }
    ```
1. 取代下列值：

    * `YOUR-KEY` 使用您的入門金鑰
    * `YOUR-ENDPOINT` 使用您的端點，例如 `westus2.api.cognitive.microsoft.com`
    * `YOUR-APP-ID` 使用您的應用程式識別碼

1. 在您建立檔案所在的相同目錄中使用命令提示字元，輸入下列命令以編譯 Java 檔案：

    ```console
    javac -cp ":lib/*" Model.java
    ```  

1. 藉由在命令提示字元中輸入下列文字，從命令列執行 Java 應用程式： 

    ```console
    java -cp ":lib/*" Model
    ```

## <a name="luis-keys"></a>LUIS 金鑰

[!INCLUDE [Use authoring key for endpoint](../includes/starter-key-explanation.md)]

## <a name="clean-up-resources"></a>清除資源

您完成本快速入門時，請從檔案系統中刪除該檔案。 

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式的最佳做法](../luis-concept-best-practices.md)