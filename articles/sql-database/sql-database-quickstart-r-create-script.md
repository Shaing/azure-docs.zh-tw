---
title: 建立和執行簡單的 R 指令碼
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在 Azure SQL Database 機器學習服務 (預覽) 中執行簡單的 R 指令碼。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: a47e7a81ba486056841bdc0fe65cfd10f1b2c412
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123200"
---
# <a name="create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>在 Azure SQL Database 機器學習服務 (預覽) 中建立和執行簡單的 R 指令碼

在本快速入門中，您會使用 Azure SQL Database 中公開預覽版的[機器學習服務 (搭配 R)](sql-database-machine-learning-services-overview.md)，建立和執行一組簡單的 R 指令碼。 您將了解如何將正確格式的 R 指令碼包裝在預存程序 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 中，以及在 SQL 資料庫中執行此指令碼。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請先[建立帳戶](https://azure.microsoft.com/free/)再開始。

- 若要在這些練習中執行範例程式碼，您必須具有已啟用機器學習服務 (搭配 R) 的 Azure SQL 資料庫。 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

- 確定您已安裝最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 您可以使用其他資料庫管理或查詢工具來執行 R 指令碼，但是在本快速入門中，您將使用 SSMS。

- 本快速入門需要您設定伺服器層級的防火牆規則。 如需如何執行這項操作的相關資訊，請參閱[建立伺服器層級的防火牆規則](sql-database-server-level-firewall-rule.md)。

## <a name="run-a-simple-script"></a>執行簡單的指令碼

若要執行 R 指令碼，您可將它當作引數傳遞至系統預存程序 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql)。

在下列步驟中，您會在 SQL 資料庫中執行此範例 R 指令碼：

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. 開啟 **SQL Server Management Studio**，然後連線至 SQL 資料庫。

   如果您需要連線方面的協助，請參閱[快速入門：使用 SQL Server Management Studio 連線和查詢 Azure SQL Database](sql-database-connect-query-ssms.md)。

1. 將完整的 R 指令碼傳遞至 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 預存程序。

   此指令碼會透過 `@script` 引數傳遞。 `@script` 引數內的所有項目都必須是有效的 R 程式碼。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   如果發生任何錯誤，有可能是因為您的 SQL 資料庫未啟用公開預覽版的機器學習服務 (搭配 R)。 請參閱上述[必要條件](#prerequisites)。

   > [!NOTE]
   > 如果您是系統管理員，您可以自動執行外部程式碼。 您可以使用以下命令，將權限授予其他使用者：
   <br>**GRANT EXECUTE ANY EXTERNAL SCRIPT TO** *\<username\>* .

2. 系統會計算正確的結果，而 R `print` 函式將結果傳回至 [訊息]  視窗。

   您應該會看到類似下面的畫面。

    **結果**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>執行 Hello World 指令碼

典型的範例指令碼是只會輸出 "Hello World" 字串的指令碼。 執行下列命令。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

此預存程序的輸入包括：

| | |
|-|-|
| @language | 定義要呼叫的語言擴充功能，在此情況下為 R |
| @script | 定義傳遞至 R 執行階段的命令。 整個 R 指令碼都必須包含在這個引數中 (作為 Unicode 文字)。 您也可以將文字新增到 **nvarchar** 類型的變數，然後呼叫此變數 |
| @input_data_1 | 查詢所傳回的資料會傳遞至 R 執行階段，而後資料再從該處傳回至 SQL Server 作為資料框架 |
|WITH RESULT SETS | 子句會定義為 SQL Server 傳回的資料表所具備的結構描述，並新增 "Hello World" 作為資料行名稱，以及新增 **int** 作為資料類型 |

此命令會輸出下列文字：

| Hello World |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>使用輸入和輸出

根據預設，[sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 接受單一資料集作為輸入，這通常是您以有效 SQL 查詢的形式提供的資料集。 然後，它會傳回單一 R 資料框架作為輸出。

我們目前使用 [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) 的預設輸入和輸出變數：**InputDataSet** 和 **OutputDataSet**。

1. 建立測試資料的小型資料表。

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. 使用 `SELECT` 陳述式來查詢資料表。
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **結果**

    ![RTestData 資料表的內容](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. 執行下列 R 指令碼。 它會使用 `SELECT` 陳述式從資料表擷取資料，將它傳遞至 R 執行階段，並傳回資料作為資料框架。 `WITH RESULT SETS` 子句會定義為 SQL Database 傳回的資料表所具備的結構描述，並新增資料行名稱 NewColName  。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **結果**

    ![從資料表傳回資料的 R 指令碼輸出](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. 我們現在會變更輸入或輸出變數的名稱。 預設輸入和輸出變數名稱為 **InputDataSet** 和 **OutputDataSet**，此指令碼會將這些名稱變更為 **SQL_in** 和 **SQL_out**：

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    請注意，R 會區分大小寫。 R 指令碼中使用的輸入和輸出變數 (**SQL_out** **SQL_in**) 必須符合使用 `@input_data_1_name` 和 `@output_data_1_name` 定義的值 (包括大小寫)。

   > [!TIP]
   > 只有一個輸入資料集可以傳入作為參數，而且您只能傳回一個資料集。 不過，您可以從 R 程式碼內呼叫其他資料集，而且可以在資料集以外傳回其他類型的輸出。 您也可以為任何參數加上 OUTPUT 關鍵字，使其與結果一起傳回。

1. 您也可以只使用 R 指令碼 (沒有任何輸入資料) 來產生值 (`@input_data_1` 會設為空白)。

   下列指令碼會輸出 "hello" 和 "world" 文字。

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **結果**

    ![使用 @script 作為輸入的查詢結果](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>檢查 R 版本

如果您想要查看安裝在 SQL 資料庫中的 R 版本，請執行下列指令碼。

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

R `print` 函式會將版本傳回至 [訊息]  視窗。 在下列範例輸出中，您可以看到此案例中的 SQL 資料庫已安裝 R 3.4.4 版。

**結果**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>列出 R 套件

Microsoft 會在提供多個隨機器學習服務預先安裝在 SQL 資料庫中的 R 套件。

若要查看已安裝的 R 套件清單 (包括版本、相依性、授權和程式庫路徑資訊)，請執行下列指令碼。

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

輸出來自於 R 中的 `installed.packages()`，並會以結果集的形式傳回。

**結果**

![R 中已安裝的套件](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>後續步驟

若要在 SQL Database 中使用 R 建立機器學習模型，請遵循本快速入門：

> [!div class="nextstepaction"]
> [使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立和定型預測模型](sql-database-quickstart-r-train-score-model.md)

如需有關「搭配 R 的 Azure SQL Database 機器學習服務 (預覽)」的詳細資訊，請參閱下列文章。

- [搭配 R 的 Azure SQL Database 機器學習服務 (預覽)](sql-database-machine-learning-services-overview.md)
- [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](sql-database-machine-learning-services-functions.md)
- [在 Azure SQL Database 機器學習服務 (預覽) 中使用 R 和 SQL 資料](sql-database-machine-learning-services-data-issues.md)
