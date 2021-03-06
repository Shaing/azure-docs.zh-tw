---
title: 使用 Visual Studio Code 在 Azure 中建立第一個函式
description: 使用 Visual Studio Code 中的 Azure Functions 擴充功能建立簡單 HTTP 觸發函式並發佈至 Azure。
ms.topic: quickstart
ms.date: 06/25/2019
ms.custom: mvc, devcenter
ms.openlocfilehash: b89e6d75cd82a65dfbce29b949c4b7d463582bb9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230786"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>使用 Visual Studio Code 建立第一個函式

Azure Functions 可讓您在[無伺服器](https://azure.microsoft.com/solutions/serverless/)環境中執行程式碼，而不需要先建立 VM 或發佈 Web 應用程式。

在本文中，您將了解如何使用[適用於 Visual Studio Code 的 Azure Functions 擴充功能]，在使用 Microsoft Visual Studio Code 的本機電腦上建立和測試 "hello world" 函式。 然後將函式程式碼從 Visual Studio Code 發佈至 Azure。

![Visual Studio 專案中的 Azure Functions 程式碼](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

擴充功能目前支援 C#、JavaScript、Java 和 Python 函式。 本文和後續文章中的步驟只支援 JavaScript 和 C# 函式。 若要了解如何使用 Visual Studio Code 來建立和發佈 Python 函式，請參閱[使用 Visual Studio Code 在 Python 中建立和部署無伺服器 Azure Functions](/azure/python/tutorial-vs-code-serverless-python-01)。 若要了解如何使用 Visual Studio Code 來建立和發佈 PowerShell 函式，請參閱[在 Azure 中建立您的第一個 PowerShell 函式](functions-create-first-function-powershell.md)。 

此擴充功能目前為預覽狀態。 若要進一步了解，請參閱[適用於 Visual Studio Code 的 Azure Functions 擴充功能]的擴充功能頁面。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

* 在其中一個[支援的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上安裝 [Visual Studio Code](https://code.visualstudio.com/)。

* 安裝 2.x 版的 [Azure Functions Core Tools](functions-run-local.md#v2)。

* 安裝所選語言的特定需求：

    | 語言 | 需求 |
    | -------- | --------- |
    | **C#** | [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>作用中 LTS 和維修 LTS 版本 (建議使用 8.11.1 和 10.14.1)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

確認函式在本機電腦上正確執行之後，就可以將專案發佈到 Azure。

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>在 Azure 中執行函式

1. 從 [輸出]  面板中複製 HTTP 觸發程序的 URL。 此 URL 包含函式金鑰，會傳至 `code` 查詢參數。 如同以往，務必將查詢字串 `?name=<yourname>` 新增至此 URL 的結尾並執行要求。

    呼叫 HTTP URL 觸發函式的 URL 應採用下列格式：

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. 將 HTTP 要求的新 URL 貼到瀏覽器的網址列。 下圖顯示瀏覽器中對於函式傳回之遠端 GET 要求所做出的回應︰ 

    ![瀏覽器中的函式回應](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>後續步驟

您已透過 Visual Studio Code，使用簡單的 HTTP 觸發函式建立函式應用程式。 在下一篇文章中，您可以藉由新增輸出繫結來展開該函式。 這個繫結會從 HTTP 要求將字串寫入 Azure 佇列儲存體佇列中的訊息。 下一篇文章也會示範如何藉由移除您所建立的資源群組，清除這些新的 Azure 資源。

> [!div class="nextstepaction"]
> [將 Azure 儲存體佇列繫結新增至您的函式](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[適用於 Visual Studio Code 的 Azure Functions 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
