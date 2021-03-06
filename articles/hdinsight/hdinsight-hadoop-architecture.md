---
title: Apache Hadoop 架構 - Azure HDInsight
description: 說明 Azure HDInsight 叢集上的 Apache Hadoop 儲存和處理。
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/28/2019
ms.openlocfilehash: 2da9e41323a308782dad509c628a3677ab0cd21f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162890"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>HDInsight 上的 Apache Hadoop 架構

[Apache Hadoop](https://hadoop.apache.org/) 包含兩個核心元件：提供儲存體的 [Apache Hadoop 分散式檔案系統 (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html)，和提供處理功能的 [Apache Hadoop Yet Another Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)。 使用儲存體和處理功能，叢集變成能夠執行 [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) 程式以進行所需的資料處理。

> [!NOTE]  
> 通常不會在 HDInsight 叢集內部署 HDFS 來提供儲存體。 相反地，Hadoop 元件使用 HDFS 相容的介面層。 實際的儲存體功能是由 Azure 儲存體或 Azure Data Lake Storage 提供。 對於 Hadoop，在 HDInsight 叢集上執行的 MapReduce 作業會像 HDFS 存在一般運作，因此不需要變更即可支援其儲存需求。 在 HDInsight 上的 Hadoop 中，儲存體是外部的，但 YARN 處理仍然是核心元件。 如需詳細資訊，請參閱 [Azure HDInsight 簡介](hadoop/apache-hadoop-introduction.md)。

本文會介紹 YARN 以及 YARN 是如何協調 HDInsight 上應用程式的執行工作。

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN 基本概念

YARN 掌管並協調 Hadoop 中的資料處理。 YARN 有兩種核心服務，在叢集節點上作為流程執行：

* ResourceManager
* NodeManager

ResourceManager 會將叢集的計算資源授與 MapReduce 作業這類應用程式。 ResourceManager 以容器的方式提供這類資源，每個容器的組成即是 CPU 核心和 RAM 記憶體的分配。 如果您結合叢集中所有可用的資源，然後將核心和記憶體分配給數個區塊，則每個資源區塊就是一個容器。 叢集中的每個節點有容量可容納一定數量的容器，因此叢集可用的容器數目有固定的限制。 容器中的資源配額可以設定。

在叢集上執行 MapReduce 應用程式時，ResourceManager 會提供要在其中執行應用程式的容器。 ResourceManager 會追蹤執行應用程式的狀態、可用的叢集容量，也會追蹤應用程式執行完成的時刻，並將資源釋出。

ResourceManager 也會執行 Web 伺服器處理序，提供可用來監控應用程式狀態的 Web 使用者介面。

當使用者提交要在叢集上執行的 MapReduce 應用程式時，會將應用程式提交至 ResourceManager。 接著，ResourceManager 會在可用的 NodeManager 節點上配置容器。 NodeManager 節點是應用程式實際執行的地方。 第一個配置的容器會執行名為 ApplicationMaster 的特殊應用程式。 此 ApplicationMaster 負責以後續容器的形式，取得執行提交之應用程式所需的資源。 ApplicationMaster 會檢查應用程式的階段 (例如，對應階段和簡化階段)，並將資料的需求量納入考量。 接著，ApplicationMaster 會代表應用程式向 ResourceManager 要求 (*交涉*) 資源。 ResourceManager 再將叢集中 NodeManagers 的資源授與 ApplicationMaster，供其在執行應用程式時使用。

NodeManagers 會執行組成應用程式的各項工作，然後將其進度和狀態回報給 ApplicationMaster。 ApplicationMaster 再將應用程式的狀態回報給 ResourceManager。 ResourceManager 會將任何結果傳回用戶端。

## <a name="yarn-on-hdinsight"></a>HDInsight 上的 YARN

所有的 HDInsight 叢集類型皆會部署 YARN。 ResourceManager 的部署是為了達到高可用性，有主要和次要執行個體，其分別在叢集內的第一個和第二個前端節點上執行。 一次只會有一個 ResourceManager 執行個體處於使用中狀態。 NodeManager 執行個體會在叢集中可用的背景工作節點上執行。

![Azure HDInsight 上的 Apache YARN](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="next-steps"></a>後續步驟

* [搭配 MapReduce 與 HDInsight 上的 Apache Hadoop](hadoop/hdinsight-use-mapreduce.md)
* [Azure HDInsight 簡介](hadoop/apache-hadoop-introduction.md)
