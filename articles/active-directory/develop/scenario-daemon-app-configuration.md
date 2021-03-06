---
title: 設定呼叫 web Api 的 daemon 應用程式-Microsoft 身分識別平臺 |Azure
description: 瞭解如何為可呼叫 web Api （應用程式設定）的背景工作應用程式設定程式碼
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88062c2134600d5b1460858c3799cfc8daa83744
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775219"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>呼叫 web Api 的 Daemon 應用程式-程式碼設定

瞭解如何為可呼叫 web Api 的背景工作應用程式設定程式碼。

## <a name="msal-libraries-that-support-daemon-apps"></a>支援 daemon 應用程式的 MSAL 程式庫

這些 Microsoft 程式庫支援 daemon 應用程式：

  MSAL 程式庫 | 說明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援 .NET Framework 和 .NET Core 平臺來建立 daemon 應用程式。 （UWP、Xamarin 和 Xamarin 不受支援，因為這些平臺是用來建立公用用戶端應用程式）。
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支援 Python 中的 daemon 應用程式。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支援 JAVA 中的 daemon 應用程式。

## <a name="configure-the-authority"></a>設定授權單位

Daemon 應用程式會使用應用程式許可權，而不是委派的許可權。 因此，其支援的帳戶類型不能是任何組織目錄中的帳戶，也不能是任何個人 Microsoft 帳戶（例如 Skype、Xbox、Outlook.com）。 沒有租使用者系統管理員可將同意授與 Microsoft 個人帳戶的 daemon 應用程式。 您必須選擇 [*我的組織中的帳戶*] 或*任何組織中的帳戶*。

因此，應用程式設定中指定的授權單位應該是租使用者（指定租使用者識別碼或與您組織相關聯的功能變數名稱）。

如果您是 ISV，而且想要提供多租使用者工具，您可以使用 `organizations`。 但請記住，您也必須向客戶說明如何授與系統管理員同意。 如需詳細資訊，請參閱[要求對整個租使用者的同意](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)。 此外，目前只有 MSAL 的限制：只有在用戶端認證是應用程式密碼（而非憑證）時，才允許 `organizations`。

## <a name="configure-and-instantiate-the-application"></a>設定並具現化應用程式

在 MSAL 程式庫中，用戶端認證（密碼或憑證）會當做機密用戶端應用程式結構的參數來傳遞。

> [!IMPORTANT]
> 即使您的應用程式是以服務形式執行的主控台應用程式，如果它是背景程式應用程式，則必須是機密用戶端應用程式。

### <a name="configuration-file"></a>組態檔

設定檔會定義：

- 授權單位或雲端實例和租使用者識別碼。
- 您從應用程式註冊所獲得的用戶端識別碼。
- 可能是用戶端密碼或憑證。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[.Net Core 主控台](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)背景程式範例中的[appsettings。](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json)

```JSon
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

您可以提供 `ClientSecret` 或 `CertificateName`。 這些設定是獨佔的。

# <a name="pythontabpython"></a>[Python](#tab/python)

當您使用用戶端密碼建立機密用戶端時， [Python](https://github.com/Azure-Samples/ms-identity-python-daemon)背景程式範例中的[參數. json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json)設定檔如下所示：

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

當您建立具有憑證的機密用戶端時， [Python](https://github.com/Azure-Samples/ms-identity-python-daemon)背景程式範例中的[參數. json](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json)設定檔如下所示：

```Json
{
  "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

[TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)是用來設定 MSAL JAVA dev 範例的類別：

```Java
public class TestData {

    final static String TENANT_SPECIFIC_AUTHORITY = "https://login.microsoftonline.com/<TenantId>/";
    final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
    final static String CONFIDENTIAL_CLIENT_ID = "";
    final static String CONFIDENTIAL_CLIENT_SECRET = "";
}
```

---

### <a name="instantiate-the-msal-application"></a>具現化 MSAL 應用程式

若要具現化 MSAL 應用程式，您必須新增、參考或匯入 MSAL 套件（視語言而定）。

根據您使用的是用戶端密碼或憑證（或是做為高階案例，已簽署的判斷提示），此結構會有所不同。

#### <a name="reference-the-package"></a>參考封裝

在您的應用程式程式碼中參考 MSAL 套件。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

將[IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet 套件新增至您的應用程式。
在 MSAL.NET 中，機密用戶端應用程式是由 `IConfidentialClientApplication` 介面表示。
在原始程式碼中使用 MSAL.NET 命名空間。

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import msal
```

# <a name="javatabjava"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>以用戶端秘密具現化機密用戶端應用程式

以下程式碼會使用用戶端秘密來具現化機密用戶端應用程式：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(TestData.CONFIDENTIAL_CLIENT_SECRET))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>以用戶端憑證具現化機密用戶端應用程式

以下是使用憑證來建立應用程式的程式碼：

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="javatabjava"></a>[Java](#tab/java)

在 MSAL JAVA 中，有兩個產生器可具現化具有憑證的機密用戶端應用程式：

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(pkcs12Certificate, certificatePassword))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

或

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

ConfidentialClientApplication app = ConfidentialClientApplication.builder(
        TestData.CONFIDENTIAL_CLIENT_ID,
        ClientCredentialFactory.create(rsaPrivateKey, publicKeyCertificate))
        .authority(TestData.TENANT_SPECIFIC_AUTHORITY)
        .build();
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Advanced 案例：使用用戶端判斷提示具現化機密用戶端應用程式

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

機密用戶端應用程式也可以使用用戶端判斷提示來證明其身分識別，而不是用戶端密碼或憑證。

MSAL.NET 有兩種方法可將已簽署的判斷提示提供給機密用戶端應用程式：

- `.WithClientAssertion()`
- `.WithClientClaims()`

當您使用 `WithClientAssertion`時，您需要提供已簽署的 JWT。 此 advanced 案例詳述于[用戶端判斷](msal-net-client-assertions.md)提示。

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

當您使用 `WithClientClaims`時，MSAL.NET 會產生一個帶正負號的判斷提示，其中包含 Azure AD 所預期的宣告，以及其他您想要傳送的用戶端宣告。
這段程式碼會示範如何執行此動作：

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();```
```

同樣地，如需詳細資訊，請參閱[用戶端判斷](msal-net-client-assertions.md)提示。

# <a name="pythontabpython"></a>[Python](#tab/python)

在 MSAL Python 中，您可以使用將由此 `ConfidentialClientApplication`的私密金鑰簽署的宣告，來提供用戶端宣告。

```Python
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

如需詳細資訊，請參閱[ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)的 MSAL Python 參考檔。

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL JAVA 處於公開預覽狀態。 尚未支援已簽署的判斷提示。

---

## <a name="next-steps"></a>後續步驟

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Daemon 應用程式-取得應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Daemon 應用程式-取得應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Daemon 應用程式-取得應用程式的權杖](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-acquire-token?tabs=java)

---
