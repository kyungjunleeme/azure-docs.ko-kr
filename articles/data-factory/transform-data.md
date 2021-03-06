---
title: 데이터 변환
description: Hadoop, Azure Machine Learning Studio (클래식) 또는 Azure Data Lake Analytics를 사용 하 여 Azure Data Factory 데이터를 변환 하거나 데이터를 처리 합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 37eac4acab7232e44f94e852b1c04c5549447b09
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637686"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory의 데이터 변환

> [!div class="op_single_selector"]
> * [매핑 데이터 흐름](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight 스트리밍](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (클래식)](transform-data-using-machine-learning.md) 
> * [저장 프로시저](transform-data-using-stored-procedure.md)
> * [데이터 레이크 분석 U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks 노트북](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 사용자 지정](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>개요
이 문서에서는 원시 데이터를 대규모의 예측 및 통찰력으로 변환 하 고 처리 하는 데 사용할 수 있는 Azure Data Factory의 데이터 변환 작업을 설명 합니다. 변환 작업은 Azure Databricks 또는 Azure HDInsight와 같은 컴퓨팅 환경에서 실행 됩니다. 각 변환 작업에 대한 자세한 정보가 있는 문서에 대한 링크를 제공합니다.

Data Factory는 개별적 또는 다른 작업과 연계하여 [파이프라인](concepts-pipelines-activities.md)에 추가할 수 있는 다음 데이터 변환 작업을 지원합니다.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>데이터 흐름을 사용 하 여 Azure Data Factory에서 기본적으로 변환

### <a name="mapping-data-flows"></a>데이터 흐름 매핑

데이터 흐름 매핑은 Azure Data Factory에서 시각적으로 디자인 된 데이터 변환입니다. 데이터 흐름을 통해 데이터 엔지니어는 코드를 작성 하지 않고도 그래픽 데이터 변환 논리를 개발할 수 있습니다. 결과 데이터 흐름은 확장 된 Spark 클러스터를 사용 하는 Azure Data Factory 파이프라인 내에서 작업으로 실행 됩니다. 기존 Data Factory 일정, 제어, 흐름 및 모니터링 기능을 통해 데이터 흐름 활동을 조작 가능한 수 있습니다. 자세한 내용은 [데이터 흐름 매핑](concepts-data-flow-overview.md)을 참조 하세요.

### <a name="wrangling-data-flows"></a>랭 글 링 데이터 흐름

Azure Data Factory의 랭 글 링 데이터 흐름을 사용 하면 클라우드 규모에서 코드 없는 데이터 준비를 반복적으로 수행할 수 있습니다. 랭 글 링 데이터 흐름은 [파워 쿼리 Online](/power-query/) 과 통합 되며 spark 실행을 통해 클라우드 규모의 데이터 랭 글 링에 사용할 수 있는 파워 쿼리 M 함수를 제공 합니다. 자세한 내용은 [랭 글 링 data](wrangling-data-flow-overview.md)flow을 참조 하세요.

## <a name="external-transformations"></a>외부 변환

필요에 따라 변환을 직접 코딩 하 고 외부 계산 환경을 직접 관리할 수 있습니다.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive 작업
Data Factory 파이프라인에서 HDInsight Hive 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Hive 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Hive 작업](transform-data-using-hadoop-hive.md) 문서를 참조하세요. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig 작업
Data Factory 파이프라인에서 HDInsight Pig 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다. 이 작업에 대한 자세한 내용은 [Pig 작업](transform-data-using-hadoop-pig.md) 문서를 참조하세요. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 작업
Data Factory 파이프라인의 HDInsight MapReduce 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 MapReduce 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [MapReduce 작업](transform-data-using-hadoop-map-reduce.md) 문서를 참조하세요.

### <a name="hdinsight-streaming-activity"></a>HDInsight 스트리밍 작업
Data Factory 파이프라인의 HDInsight 스트리밍 작업은 사용자 고유 또는 주문형 Windows/Linux 기반 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 작업에 대한 자세한 내용은 [HDInsight 스트리밍 작업](transform-data-using-hadoop-streaming.md)을 참조하세요.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark 작업
Data Factory 파이프라인에서 HDInsight Spark 작업은 사용자 고유 HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 자세한 내용은 [Azure Data Factory에서 Spark 프로그램 호출](transform-data-using-spark.md) 을 참조하세요. 

### <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (클래식) 활동
Azure Data Factory를 사용 하면 예측 분석을 위해 게시 된 Azure Machine Learning Studio (클래식) 웹 서비스를 사용 하는 파이프라인을 쉽게 만들 수 있습니다. Azure Data Factory 파이프라인에서 [일괄 처리 실행 작업](transform-data-using-machine-learning.md) 을 사용 하 여 Studio (클래식) 웹 서비스를 호출 하 여 일괄 처리에서 데이터에 대 한 예측을 만들 수 있습니다.

시간이 지남에 따라 스튜디오 (클래식) 점수 매기기 실험의 예측 모델은 새 입력 데이터 집합을 사용 하 여 다시 학습 해야 합니다. 재 학습을 완료 한 후에는 다시 학습 machine learning 모델을 사용 하 여 점수 매기기 웹 서비스를 업데이트 하려고 합니다. [업데이트 리소스 작업](update-machine-learning-models.md)을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다.  

이러한 Studio (클래식) 활동에 대 한 자세한 내용은 [Azure Machine Learning Studio (클래식) 활동 사용](transform-data-using-machine-learning.md) 을 참조 하세요. 

### <a name="stored-procedure-activity"></a>저장 프로시저 작업
Data Factory 파이프라인에서 SQL Server 저장 프로시저 작업을 사용 하 여 엔터프라이즈 또는 Azure VM의 데이터 저장소 Azure SQL Database, Azure Synapse Analytics (이전의 SQL Data Warehouse), SQL Server 데이터베이스 중 하나에서 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 [저장 프로시저 작업](transform-data-using-stored-procedure.md) 문서를 참조하세요.  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 작업
Data Lake Analytics U-SQL 작업은 Azure Data Lake Analytics 클러스터에 대해 U-SQL 스크립트를 실행합니다. 자세한 내용은 [Data Analytics U-SQL 작업](transform-data-using-data-lake-analytics.md) 문서를 참조하세요. 

### <a name="databricks-notebook-activity"></a>Databricks Notebook 활동

Data Factory 파이프라인의 Azure Databricks 노트북 작업은 Azure Databricks 작업 영역에서 Databricks 노트북을 실행 합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Databricks Notebook을 실행하여 데이터 변환](transform-data-databricks-notebook.md)을 참조하세요.

### <a name="databricks-jar-activity"></a>Databricks Jar 활동

Data Factory 파이프라인의 Azure Databricks Jar 활동은 Azure Databricks 클러스터에서 Spark Jar를 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Jar 활동을 실행하여 데이터 변환](transform-data-databricks-jar.md)을 참조하세요.

### <a name="databricks-python-activity"></a>Databricks Python 활동

Data Factory 파이프라인의 Azure Databricks Python 활동은 Azure Databricks 클러스터에서 Python 파일을 실행합니다. Azure Databricks는 Apache Spark를 실행하기 위해 관리되는 플랫폼입니다. [Azure Databricks에서 Python 활동을 실행하여 데이터 변환](transform-data-databricks-python.md)을 참조하세요.

### <a name="custom-activity"></a>사용자 지정 작업
Data Factory에서 지원되지 않는 방식으로 데이터를 변환해야 하는 경우 고유의 데이터 이동 논리가 포함된 사용자 지정 작업을 만들어서 파이프라인에 해당 작업을 사용할 수 있습니다. Azure Batch 서비스 또는 Azure HDInsight 클러스터를 사용하여 실행되도록 사용자 지정 .NET 작업을 구성할 수 있습니다. 자세한 내용은 [사용자 지정 작업 사용](transform-data-using-dotnet-custom-activity.md) 문서를 참조하세요. 

R이 설치된 HDInsight 클러스터에서 R 스크립트를 실행하는 사용자 지정 작업을 만들 수 있습니다. [Azure Data Factory를 사용하여 R 스크립트 실행](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)을 참조하세요. 

### <a name="compute-environments"></a>컴퓨팅 환경
컴퓨팅 환경을 위한 연결된 서비스를 만들고 변환 작업을 정의할 때 이 연결된 서비스를 사용합니다. 데이터 팩터리에서 지원하는 컴퓨팅 환경은 두 가지 유형이 있습니다. 

- **주문형** : 이 경우 데이터 팩터리에서 완전히 컴퓨팅 환경을 관리합니다. 데이터를 처리하기 위한 작업을 제출하기 전에 데이터 팩터리 서비스에서 자동으로 컴퓨팅 환경을 만들고 작업이 완료되면 제거합니다. 작업 실행, 클러스터 관리, 부트스트래핑 작업에 대한 주문형 컴퓨팅 환경의 세부적인 설정을 구성 및 제어할 수 있습니다. 
- **자체 환경 사용** : 이 경우 사용자 고유의 컴퓨팅 환경(예: HDInsight 클러스터)을 데이터 팩터리에 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 이를 사용하여 작업을 실행하는 데이터 팩터리 서비스와 사용자에 의해 관리됩니다. 

데이터 팩터리에서 지원하는 컴퓨팅 서비스에 대한 자세한 내용은 [컴퓨팅 연결 서비스](compute-linked-services.md) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계
변환 작업 사용 예제에 대해서는 [자습서: Spark를 사용하여 데이터 변환](tutorial-transform-data-spark-powershell.md)을 참조하세요.