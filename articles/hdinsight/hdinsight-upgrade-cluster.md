---
title: 將 HDInsight 叢集升級為更新的版本 - Azure
description: 瞭解將您的 Azure HDInsight 叢集升級為較新版本的指導方針。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951151"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>將 HDInsight 叢集升級為更新的版本

若要充分利用最新的 HDInsight 功能，建議您將 HDInsight 叢集升級到最新的版本。 依照下面的指導方針升級您的 HDInsight 叢集版本。

> [!NOTE]  
> 如需受支援 HDInsight 版本的詳細資訊，請參閱 [HDInsight 元件版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。

## <a name="upgrade-tasks"></a>升級工作

升級 HDInsight 叢集的工作流程如下。
![HDInsight 升級工作流程圖表](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. 閱讀此文件的每一節，以了解在升級 HDInsight 叢集時，可能需要進行的變更。
2. 將叢集建立為測試/品質保證環境。 如需建立叢集的詳細資訊，請參閱[了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
3. 將現有的作業、資料來源與接收複製到新的環境。
4. 執行驗證測試以確保您的工作在新叢集上會如預期般運作。

當您已驗證一切都會如預期般運作之後，請為移轉排定停機時間。 在此停機期間，請執行下列動作：

1. 備份所有儲存在本機叢集節點上的暫時性資料。 例如，如果您的資料是直接儲存在前端節點上。
1. [刪除現有的](./hdinsight-delete-cluster.md)叢集。
1. 使用與先前叢集所使用之預設資料存放區相同的資料存放區，在具有最新 (或受支援) 之 HDI 版本的 VNET 子網路中建立叢集。 這將能允許新叢集針對現有的生產資料繼續運作。
1. 匯入任何已備份的暫時性資料。
1. 使用新叢集啟動工作/繼續處理。

## <a name="next-steps"></a>後續步驟

* [了解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 SSH 連線到 HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Apache Ambari 管理 Linux 型叢集](hdinsight-hadoop-manage-ambari.md)
* [HDInsight 版本資訊](./hdinsight-version-release.md)
