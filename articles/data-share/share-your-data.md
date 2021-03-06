---
title: 教學課程：在貴組織外部共用 - Azure Data Share
description: 教學課程 - 使用 Azure Data Share 與客戶及合作夥伴共用資料
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 8749f7dee2ceeb09e37cc97d4e5bfe76c52e2da6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438746"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>教學課程：使用 Azure Data Share 共用資料  

在本教學課程中，您會了解如何設定新的 Azure Data Share，並開始與 Azure 組織外的客戶及合作夥伴共用資料。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 建立 Data Share。
> * 將資料集新增至 Data Share。
> * 為 Data Share 啟用同步處理排程。 
> * 將收件者新增至 Data Share。 

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶：如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 您收件者的 Azure 登入電子郵件地址 (不能使用其電子郵件別名)。

### <a name="share-from-a-storage-account"></a>從儲存體帳戶共用：

* Azure 儲存體帳戶：如果您還沒有此帳戶，則可以建立 [Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 將角色指派新增至儲存體帳戶的權限，其存在於 Microsoft.Authorization/role assignments/write  權限中。 此權限存在於擁有者角色中。 

### <a name="share-from-a-sql-based-source"></a>從 SQL 型來源共用：

* Azure SQL Database 或 Azure SQL 資料倉儲，具有您想要共用的資料表和檢視。
* 存取資料倉儲的資料共用權限。 這可以透過下列步驟完成： 
    1. 將自己設定為伺服器的 Azure Active Directory 系統管理員。
    1. 使用 Azure Active Directory 連線到 Azure SQL Database/資料倉儲。
    1. 使用查詢編輯器 (預覽) 來執行下列指令碼，將 Data Share MSI 新增為 db_owner。 您必須使用 Active Directory 連線，而不是使用 SQL Server 驗證。 
    
```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```                   
請注意， *<share_acc_name>* 是您 Data Share 帳戶的名稱。 如果您尚未建立 Data Share 帳戶，您可以稍後再回到此先決條件。  

* 具有 [`db_owner` 存取權](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins#non-administrator-users)的 Azure SQL Database 使用者，可瀏覽並選取您想共用的資料表和/或檢視。 

* 用戶端 IP SQL Server 防火牆存取：這可以透過下列步驟完成：1. 瀏覽至 [防火牆與虛擬網路]  。 按一下 [開啟]  切換開關，以允許存取 Azure 服務。 

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-data-share-account"></a>建立 Data Share 帳戶

在 Azure 資源群組中建立 Azure Data Share 資源。

1. 選取入口網站左上角的 [建立資源]  按鈕 (+)。

1. 搜尋「Data Share」  。

1. 選取 [Data Share]，然後選取 [建立]  。

1. 使用下列資訊填寫 Azure Data Share 資源的基本詳細資料。 

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 名稱 | datashareacount  | 指定資料共用帳戶的名稱。 |
    | 訂用帳戶 | 您的訂用帳戶 | 選取您要用於資料共用帳戶的 Azure 訂用帳戶。|
    | 資源群組 | *test-resource-group* | 使用現有資源群組，或建立新的資源群組。 |
    | Location | 美國東部 2  | 選取資料共用帳戶的區域。
    | | |

1. 選取 [建立]  來佈建資料共用帳戶。 佈建新的資料共用帳戶一般需要大約不到 2 分鐘的時間。 

1. 部署完成後，請選取 [移至資源]  。

## <a name="create-a-data-share"></a>建立 Data Share

1. 瀏覽至 [Data Share 概觀] 頁面。

    ![共用您的資料](./media/share-receive-data.png "共用您的資料") 

1. 選取 [開始共用資料]  。

1. 選取 [建立]  。   

1. 填寫 Data Share 的詳細資料。 指定名稱、共用內容的說明和使用規定 (選擇性)。 

    ![EnterShareDetails](./media/enter-share-details.png "輸入共用詳細資料") 

1. 選取 [繼續] 

1. 若要將資料集新增至 Data Share，請選取 [新增資料集]  。 

    ![資料集](./media/datasets.png "資料集")

1. 選取您想要新增的資料集類型。 如果從 Azure SQL Database 或 Azure SQL 資料倉儲共用，系統會提示您提供一些 SQL 認證。 使用您建立為必要條件的使用者進行驗證。

    ![AddDatasets](./media/add-datasets.png "新增資料集")    

1. 瀏覽至您想要共用的物件，然後選取 [新增資料集]。 

    ![SelectDatasets](./media/select-datasets.png "選取資料集")    

1. 在 [收件者] 索引標籤中，藉由選取 [+ 新增收件者] 來輸入資料取用者的電子郵件地址。 

    ![AddRecipients](./media/add-recipient.png "新增收件者") 

1. 選取 [繼續] 

1. 如果您想要讓資料取用者能夠取得資料的累加更新，請啟用快照集排程。 

    ![EnableSnapshots](./media/enable-snapshots.png "啟用快照集") 

1. 選取開始時間和週期間隔。 

1. 選取 [繼續] 

1. 在 [檢閱 + 建立] 索引標籤中，檢閱 [套件內容]、[設定]、[收件者] 及 [同步處理設定]。 選取 [建立] 

Azure Data Share 現已建立完成，而且 Data Share 的收件者現已準備好接受邀請。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立 Azure Data Share 並邀請收件者。 若要了解資料取用者要如何接受和接收資料共用，請繼續閱讀[接受和接收資料](subscribe-to-data-share.md)教學課程。 
