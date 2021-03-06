---
title: ETL 대신 ELT 설계
description: Azure Synapse Analytics 내에서 전용 SQL 풀에 대 한 유연한 데이터 로드 전략을 구현 합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 64ba24eb0eab581310122908fc05d1d671ac1d40
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531576"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대 한 데이터 로드 전략

기존의 SMP 전용 SQL 풀은 데이터 로드를 위한 ETL (추출, 변환 및 로드) 프로세스를 사용 합니다. Synapse SQL은 Azure Synapse Analytics 내에서 계산 및 저장소 리소스의 확장성과 유연성을 활용 하는 분산 쿼리 처리 아키텍처를 사용 합니다.

ELT (추출, 로드 및 변환) 프로세스를 사용 하 여 기본 제공 분산 쿼리 처리 기능을 활용 하 고 로드 하기 전에 데이터 변환에 필요한 리소스를 제거 합니다.

전용 SQL 풀은 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 및 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)와 같은 인기 있는 SQL Server 옵션을 포함 하 여 많은 로드 방법을 지원 하지만 데이터를 로드 하는 가장 빠르고 확장성 있는 방법은 PolyBase 외부 테이블 및 [COPY 문을](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)사용 하는 것입니다.

PolyBase와 COPY 문을 사용하면 T-SQL 언어를 통해 Azure Blob Storage 또는 Azure Data Lake Store에 저장된 외부 데이터에 액세스할 수 있습니다. 로드 시 유연성을 극대화하려면 COPY 문을 사용하는 것이 좋습니다.


## <a name="what-is-elt"></a>ELT란 무엇인가요?

ELT (추출, 로드 및 변환)는 원본 시스템에서 데이터를 추출 하 여 전용 SQL 풀에 로드 한 다음 변환 하는 프로세스입니다.

ELT를 구현하는 기본 단계는 다음과 같습니다.

1. 원본 데이터를 텍스트 파일로 추출합니다.
2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터를 둡니다.
3. 로드할 데이터를 준비합니다.
4. PolyBase 또는 COPY 문을 사용하여 준비 테이블로 데이터를 로드합니다.
5. 데이터를 변환합니다.
6. 프로덕션 테이블에 데이터를 삽입합니다.

로드 자습서는 [Azure blob storage에서 데이터 로드](load-data-from-azure-blob-storage-using-polybase.md)를 참조 하세요.

## <a name="1-extract-the-source-data-into-text-files"></a>1. 텍스트 파일에 원본 데이터 추출

원본 시스템에서 데이터를 추출하는 방식은 스토리지 위치에 따라 달라집니다. 데이터를 지원 되는 구분 된 텍스트 또는 CSV 파일로 이동 하는 것이 목표입니다.

### <a name="supported-file-formats"></a>지원되는 파일 형식

PolyBase 및 COPY 문을 사용하면 UTF-8 및 UTF-16으로 인코딩된 구분된 텍스트 또는 CSV 파일에서 데이터를 로드할 수 있습니다. 구분된 텍스트 또는 CSV 파일 외에도 ORC, Parquet과 같은 Hadoop 파일 형식에서도 데이터를 로드하며, 또한 Gzip 및 Snappy 압축 파일에서도 데이터를 로드합니다.

확장 ASCII, 고정 너비 형식 및 중첩된 형식(예: WinZip, XML)은 지원되지 않습니다. SQL Server에서 내보내는 경우에는 [bcp 명령줄 도구](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)를 사용하여 구분된 텍스트 파일로 데이터를 내보낼 수 있습니다.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Azure Blob Storage 또는 Azure Data Lake Store에 데이터 저장

Azure 스토리지에 데이터를 두려면 [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 또는 [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)로 이동할 수 있습니다. 어느 위치에서든 데이터를 텍스트 파일에 저장해야 합니다. PolyBase 및 COPY 문은 두 위치 중 어디서든 데이터를 로드할 수 있습니다.

데이터를 Azure Storage로 이동하는 데 사용할 수 있는 도구와 서비스는 다음과 같습니다.

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 서비스는 네트워크 처리량, 성능 및 예측 가능성을 개선합니다. ExpressRoute는 Azure에 대한 전용 프라이빗 연결을 통해 데이터를 라우팅하는 서비스입니다. ExpressRoute 연결은 공용 인터넷을 통해 데이터를 라우팅하지 않습니다. 이 연결은 공용 인터넷을 통한 일반적인 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.
- [AZCopy 유틸리티](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)는 공용 인터넷을 통해 Azure Storage로 데이터를 이동합니다. 이는 데이터 크기가 10TB 미만인 경우에 작동합니다. AZCopy를 사용하여 정기적으로 로드를 수행하려면 네트워크 속도를 테스트하여 가능한지 확인하세요.
- [ADF(Azure Data Factory)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에는 로컬 서버에 설치할 수 있는 게이트웨이가 있습니다. 그런 다음 파이프라인을 만들어 로컬 서버에서 Azure Storage로 데이터를 이동할 수 있습니다. 전용 SQL 풀에서 Data Factory를 사용 하려면 [전용 sql 풀의 데이터 로드](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조 하세요.

## <a name="3-prepare-the-data-for-loading"></a>3. 로드할 데이터 준비

데이터를 로드하기 전에 스토리지 계정에서 데이터를 준비하고 정리해야 할 수 있습니다. 데이터가 원본에 있는 동안, 데이터를 텍스트 파일로 내보낼 때 또는 Azure Storage로 이동한 후 데이터 준비를 수행할 수 있습니다.  가능한 경우 프로세스의 초기에 데이터로 작업하는 것이 가장 쉽습니다.  

### <a name="define-the-tables"></a>테이블 정의

COPY 문을 사용할 때 먼저 전용 SQL 풀에서 로드 하는 테이블을 정의 해야 합니다.

PolyBase를 사용 하는 경우에는 로드 하기 전에 전용 SQL 풀에서 외부 테이블을 정의 해야 합니다. PolyBase는 외부 테이블을 사용하여 Azure Storage의 데이터를 정의하고 액세스합니다. 데이터베이스 뷰와 비슷한 외부 테이블은 외부 테이블은 테이블 스키마를 포함 하며 전용 SQL 풀 외부에 저장 된 데이터를 가리킵니다.

외부 테이블 정의에는 데이터 원본, 텍스트 파일 형식 및 테이블 정의를 지정하는 것이 포함됩니다. 다음은 필요한 T-SQL 구문 참조 아티클입니다.

- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Parquet 파일을 로드할 때 다음 SQL 데이터 형식 매핑을 사용 합니다.

|                         Parquet 형식                         |   Parquet 논리 형식(주석)   |  SQL 데이터 형식   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLEAN                            |                                       |       bit        |
|                     BINARY / BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      float       |
|                            FLOAT                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                STRING                 |     nvarchar     |
|                            BINARY                            |                 ENUM                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | uniqueidentifier |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVAL                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16,   true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8,   false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32,   false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME(MILLIS )             |       time       |
|                            INT64                             |            INT(64,   true)            |      bigint      |
|                            INT64                             |           INT(64, false  )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME(MILLIS)                 |       time       |
|                            INT64                             | TIMESTAMP (MILLIS)                  |    datetime2     |
| [복합 형식](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 명단 등록                  |   varchar(max)   |
| [복합 형식](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAP                  |   varchar(max)   |

>[!IMPORTANT] 
>- SQL 전용 풀은 현재 마이크로 s 및 NANOS 정밀도로 Parquet 데이터 형식을 지원 하지 않습니다. 
>- Parquet와 SQL 간에 형식이 일치 하지 않거나 Parquet 데이터 형식이 지원 되지 않는 경우 다음과 같은 오류가 발생할 수 있습니다. **"HdfsBridge:: recordReaderFillBuffer-레코드 판독기 버퍼를 채우는 동안 예기치 않은 오류 발생: ClassCastException: ..."**
>- 0-127 범위 밖의 값을 Parquet 및 ORC 파일 형식에 대 한 tinyint 열로 로드 하는 것은 지원 되지 않습니다.

외부 개체를 만드는 예제는 [외부 테이블 만들기](https://docs.microsoft.com/azure/synapse-analytics/sql/develop-tables-external-tables?tabs=sql-pool)를 참조 하세요.

### <a name="format-text-files"></a>텍스트 파일 형식 지정

PolyBase를 사용하는 경우 정의된 외부 개체는 텍스트 파일의 행을 외부 테이블 및 파일 형식 정의와 정렬해야 합니다. 텍스트 파일의 각 행에 있는 데이터는 테이블 정의와 정렬되어야 합니다.
텍스트 파일의 형식을 지정하려면:

- 데이터가 비관계형 원본에서 오는 경우 행과 열로 변환해야 합니다. 데이터가 관계형 원본에서 오건 비관계형 원본에서 오건, 데이터를 로드하려고 하는 테이블에 대한 열 정의와 정렬되도록 데이터를 변환해야 합니다.
- 대상 테이블의 열 및 데이터 형식과 정렬되도록 텍스트 파일에서 데이터의 형식을 지정합니다. 외부 텍스트 파일 및 전용 SQL 풀 테이블의 데이터 형식에 대 한 불일치는 로드 하는 동안 행이 거부 됩니다.
- 종결자로 텍스트 파일의 필드를 구분합니다.  원본 데이터에서 찾을 수 없는 문자 또는 문자 시퀀스를 사용해야 합니다. [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)으로 지정한 종결자를 사용하세요.

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. PolyBase 또는 COPY 문을 사용하여 데이터 로드

데이터는 준비 테이블로 로드하는 것이 가장 좋습니다. 준비 테이블을 사용하면 프로덕션 테이블에 영향을 주지 않고 오류를 처리할 수 있습니다. 또한 준비 테이블을 사용 하면 데이터를 프로덕션 테이블에 삽입 하기 전에 전용 SQL 풀 병렬 처리 아키텍처를 데이터 변환에 사용할 수 있습니다.

### <a name="options-for-loading"></a>로드 옵션

데이터를 로드 하려면 다음 로드 옵션 중 하나를 사용할 수 있습니다.

- [COPY 문은](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) 데이터를 원활 하 고 유연 하 게 로드할 수 있도록 권장 되는 로드 유틸리티입니다. 이 문에는 PolyBase에서 제공 하지 않는 많은 추가 로드 기능이 있습니다. 
- [T-sql을 사용 하는 PolyBase](load-data-from-azure-blob-storage-using-polybase.md) 를 사용 하려면 외부 데이터 개체를 정의 해야 합니다.
- [ADF(Azure Data Factory)를 이용한 PolyBase 및 COPY 문](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)은 또 다른 오케스트레이션 도구입니다.  파이프라인을 정의하고 작업을 예약합니다.
- [SSIS를 사용 하는 PolyBase](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 는 원본 데이터가 SQL Server 때 잘 작동 합니다. SSIS는 대상 테이블 매핑에 대해 원본을 정의하고 로드를 조정합니다. 이미 SSIS 패키지가 있는 경우 새 데이터 웨어하우스 대상으로 작업하도록 패키지를 수정할 수 있습니다.
- [PolyBase와 Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)는 PolyBase를 사용하여 테이블에서 Databricks 데이터 프레임으로 데이터를 전송하거나 Databricks 데이터 프레임에서 테이블로 데이터를 씁니다.

### <a name="other-loading-options"></a>기타 로드 옵션

PolyBase와 COPY 문 외에도 [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)나 [SqlBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 사용할 수 있습니다. bcp는 Azure Blob Storage를 거치지 않고 데이터베이스에 직접 데이터를 로드하며, 소규모 로드에만 사용됩니다.

> [!NOTE]
> 이러한 옵션의 로드 성능은 PolyBase 및 COPY 문보다 훨씬 느립니다.

## <a name="5-transform-the-data"></a>5. 데이터 변환

데이터가 준비 테이블에 있는 동안, 워크로드에 필요한 변환을 수행하세요. 그런 다음 데이터를 프로덕션 테이블로 이동하세요.

## <a name="6-insert-the-data-into-production-tables"></a>6. 프로덕션 테이블에 데이터 삽입

INSERT INTO ... SELECT 문은 스테이징 테이블에서 영구 테이블로 데이터를 이동합니다.

ETL 프로세스를 설계할 때 작은 테스트 샘플에서 프로세스를 실행해 보세요. 테이블에서 파일로 1000개 행을 추출하여 Azure로 이동한 다음 준비 테이블에 로드해 보세요.

## <a name="partner-loading-solutions"></a>파트너 로드 솔루션

상당수의 파트너에게는 로드 솔루션이 있습니다. 자세한 내용을 알아보려면 [솔루션 파트너](sql-data-warehouse-partner-business-intelligence.md) 목록을 참조하세요.

## <a name="next-steps"></a>다음 단계

설명서를 로드하려면 [데이터를 로드하기 위한 지침](guidance-for-loading-data.md)을 참조하세요.
