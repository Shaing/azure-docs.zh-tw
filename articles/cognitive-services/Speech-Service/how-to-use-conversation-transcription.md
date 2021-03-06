---
title: 即時對話轉譯（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何搭配語音 SDK 使用即時對話轉譯。 適用于C++、 C#和 JAVA。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: 64a9e11cec7164fb4421dd018238de9f0670382b
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263722"
---
# <a name="real-time-conversation-transcription-preview"></a>即時對話轉譯（預覽）

語音 SDK 的**ConversationTranscriber** API 可讓您透過使用 `PullStream` 或 `PushStream`，將音訊串流至語音服務，以轉譯會議和其他交談的功能來新增、移除和識別多個參與者。 本主題會要求您瞭解如何搭配語音 SDK （1.8.0 或更新版本）使用語音轉換文字。 如需詳細資訊，請參閱[什麼是語音服務](overview.md)。

## <a name="limitations"></a>限制

- Windows、Linux 和 Android 上C++的C#、和 JAVA 都支援 ConversationTranscriber API。
- 目前可在下欄區域中的 "en-us" 和 "zh-CN" 語言中使用： _centralus_和_eastasia_。
- 需要具有播放參照資料流程的7個 mic 迴圈多麥克風陣列。 麥克風陣列應符合[我們的規格](https://aka.ms/sdsdk-microphone)。
- [語音裝置 SDK](speech-devices-sdk.md)提供適合的裝置，以及示範對話轉譯的範例應用程式。

## <a name="optional-sample-code-resources"></a>選擇性的範例程式碼資源

語音裝置 SDK 提供 JAVA 的範例程式碼，可使用8個通道進行即時音訊捕捉。

- [ROOBO 裝置範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/Conversation.java)
- [Azure Kinect 開發工具組範例程式碼](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>必要條件

語音服務訂用帳戶。 您可以[取得語音試用版訂用](https://azure.microsoft.com/try/cognitive-services/)帳戶（如果您沒有的話）。

## <a name="create-voice-signatures"></a>建立語音簽名

第一個步驟是為交談參與者建立語音簽名，以有效率地說話者識別。

### <a name="audio-input-requirements"></a>音訊輸入需求

- 用於建立語音簽名的輸入音訊 wave 檔案應該是16位樣本、16 kHz 採樣速率和單一通道（mono）格式。
- 每個音訊樣本的建議長度介於三十秒到2分鐘之間。

### <a name="sample-code"></a>範例程式碼

下列範例示範使用中C#[的 REST API](https://aka.ms/cts/signaturegenservice)來建立語音簽名的兩種不同方式。 請注意，您必須以「YourSubscriptionKey」的真實資訊、「speakerVoice」的 wave 檔案名，以及 `{region}` 和「YourServiceRegion」（_centralus_或_eastasia_）的區域取代。

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>轉譯交談

下列範例程式碼示範如何即時轉譯三個喇叭的交談。 它假設您已經為每個說話者建立語音簽名，如上所示。 建立 SpeechConfig 物件時，請將 "YourSubscriptionKey" 和 "YourServiceRegion" 的實際資訊替換為。

範例程式碼重點包括：

- 使用以 `Guid.NewGuid()` 產生的會議識別碼，從 `SpeechConfig` 物件建立 `Conversation` 物件
- 建立 `ConversationTranscriber` 物件，並加入與 `JoinConversationAsync()` 的交談以開始轉譯
- 註冊感關注的事件
- 使用交談物件在交談中加入或移除參與者
- 串流處理音訊
- 在語音 SDK 版本1.9.0 中，[語音簽名版本] 欄位支援 `int` 和 `string` 數值型別。

轉譯和說話者識別碼會回到已註冊的事件中。

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [非同步對話轉譯](how-to-async-conversation-transcription.md)
