---
title: Synapse Studio 노트북
description: 이 문서에서는 데이터 준비 및 시각화를 수행 하기 위해 Azure Synapse Studio 노트북을 만들고 개발 하는 방법에 대해 알아봅니다.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: bfb822481e00e84fa17bd4b1fcb58c1d4b89a124
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450861"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse Studio 노트북 만들기, 개발 및 유지 관리

Synapse Studio 노트북은 라이브 코드, 시각화 및 설명을 포함 하는 파일을 만들 수 있는 웹 인터페이스입니다. Notebook은 아이디어를 검증하고 빠른 실험을 사용하여 데이터를 통해 인사이트를 확보하기 좋은 도구입니다. Notebook은 데이터 준비, 데이터 시각화, 기계 학습 및 기타 빅 데이터 시나리오에서도 널리 사용됩니다.

Azure Synapse Studio Notebook을 사용하면 다음이 가능합니다.

* 시작에 필요한 설정 작업이 전혀 없습니다.
* 기본 제공 엔터프라이즈 보안 기능으로 데이터 보안을 유지합니다.
* 원시 형식(CSV, txt, JSON 등), 처리된 파일 형식(parquet, Delta Lake, ORC 등), SQL 및 Spark에 대한 SQL 테이블 형식 데이터 파일 모두에서 데이터를 분석합니다.
* 향상된 제작 기능 및 기본 제공 데이터 시각화를 통해 생산성을 높일 수 있습니다.

이 문서에서는 Azure Synapse Studio에서 Notebook을 사용하는 방법을 설명합니다.

## <a name="preview-of-the-new-notebook-experience"></a>새 노트북 환경 미리 보기
Synapse 팀은 새로운 노트북 구성 요소를 Synapse Studio로 가져와서 Microsoft 고객을 위한 일관 된 노트북 환경을 제공 하 고 검색 가능성, 생산성, 공유 및 공동 작업을 극대화 합니다. 새 노트북 환경을 미리 볼 준비가 되었습니다. 노트북 도구 모음의 **미리 보기 기능** 단추를 선택 하 여 설정 합니다. 다음 표에서는 기존 노트북 ("클래식 노트북" 이라고 함) 간의 기능 비교를 캡처하여 새 미리 보기를 사용 합니다.  

|기능|클래식 노트북|노트북 미리 보기|
|--|--|--|
|% 실행| 지원되지 않음 | &#9745;|
|% 기록| 지원되지 않음 |&#9745;
|% 로드| 지원되지 않음 |&#9745;|
|%% html| 지원되지 않음 |&#9745;|
|끌어서 놓고 셀 이동| 지원되지 않음 |&#9745;|
|영구 디스플레이 () 출력|&#9745;| 사용할 수 없음 |
|모두 취소| &#9745;| 사용할 수 없음|
|위의 모든 셀 실행|&#9745;| 사용할 수 없음 |
|아래의 모든 셀 실행|&#9745;| 사용할 수 없음 |
|도구 모음 단추를 사용하여 텍스트 셀 서식 지정|&#9745;| 사용할 수 없음 |
|셀 실행 취소 작업| &#9745;| 사용할 수 없음 |


## <a name="create-a-notebook"></a>Notebook 만들기

Notebook을 만드는 방법은 두 가지입니다. **개체 탐색기** 에서 새 Notebook을 만들거나 Azure Synapse 작업 영역으로 기존 Notebook을 가져올 수 있습니다. Azure Synapse Studio Notebook은 표준 Jupyter Notebook IPYNB 파일을 인식할 수 있습니다.

![가져오기 노트북 만들기](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Notebook 개발

노트북은 셀로 구성됩니다. 셀은 독립적으로 또는 그룹으로 실행할 수 있는 개별 코드 또는 텍스트 블록입니다.

### <a name="add-a-cell"></a>셀 추가

Notebook에 새 셀을 추가하는 방법은 여러 가지입니다.

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

1. 왼쪽 위에 있는 **+ 셀** 단추를 펼치고 **코드 셀 추가** 또는 **텍스트 셀 추가** 를 선택합니다.

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 두 셀 사이의 공간을 마우스로 가리키고 **코드 추가** 또는 **텍스트 추가** 를 선택합니다.

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. 현재 셀 위에 셀을 삽입하려면 **A** 를 누릅니다. 현재 셀 아래에 셀을 삽입하려면 **B** 를 누릅니다.


# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

1. 왼쪽 위 **+ 셀** 단추를 확장 하 고 **코드 셀** 또는 **Markdown cell** 을 선택 합니다.
    ![추가-azure--셀-셀-단추](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. 셀의 시작 부분에 있는 더하기 기호를 선택 하 고 **코드 셀** 또는 **Markdown cell** 을 선택 합니다.

    ![-azure-노트북-공간 간](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. [명령 모드에서 Aznb 바로 가기 키](#shortcut-keys-under-command-mode)를 사용 합니다. 현재 셀 위에 셀을 삽입하려면 **A** 를 누릅니다. 현재 셀 아래에 셀을 삽입하려면 **B** 를 누릅니다.

---

### <a name="set-a-primary-language"></a>주 언어 설정

Azure Synapse Studio Notebook은 네 가지 Apache Spark 언어를 지원합니다.

* pySpark(Python)
* Spark(Scala)
* SparkSQL
* .NET for Apache Spark(C#)

새로 추가한 셀의 주 언어는 위쪽 명령 모음의 드롭다운 목록에서 설정할 수 있습니다.

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>여러 언어 사용

셀 시작 부분에 올바른 언어 매직 명령을 지정하면 Notebook 하나에서 여러 언어를 사용할 수 있습니다. 셀 언어를 전환하는 매직 명령은 다음 표에 나열되어 있습니다.

|매직 명령 |언어 | Description |  
|---|------|-----|
|%%pyspark| Python | Spark 컨텍스트에 대해 **Python** 쿼리를 실행합니다.  |
|%%spark| 스칼라 | Spark 컨텍스트에 대해 **Scala** 쿼리를 실행합니다.  |  
|%%sql| SparkSQL | Spark 컨텍스트에 대해 **SparkSQL** 쿼리를 실행합니다.  |
|%%csharp | .NET for Spark C# | Spark 컨텍스트에 대해 **.NET for Spark C#** 쿼리를 실행합니다. |

다음 이미지는 **Spark(Scala)** Notebook에서 **%%pyspark** 매직 명령을 사용하여 PySpark 쿼리를 작성하거나 **%%sql** 매직 명령을 사용하여 SparkSQL 쿼리를 작성하는 방법의 예입니다. Notebook의 주 언어는 pySpark로 설정되어 있습니다.

   ![Synapse spark 매직 명령](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>임시 테이블을 사용하여 언어 간 데이터 참조

Synapse Studio Notebook의 여러 언어 간에 직접 데이터나 변수를 참조할 수 없습니다. Spark에서는 여러 언어 간에 임시 테이블을 참조할 수 있습니다. 다음은 차선책으로 Spark temp 테이블을 사용하여 `PySpark`와 `SparkSQL`에서 `Scala` DataFrame을 읽는 방법의 예입니다.

1. 1 셀에서 Scala을 사용 하 여 SQL 풀 커넥터에서 데이터 프레임를 읽고 임시 테이블을 만듭니다.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. 셀 2에서는 Spark SQL을 사용하여 데이터를 쿼리합니다.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 셀 3에서는 PySpark의 데이터를 사용합니다.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 스타일 IntelliSense

Azure Synapse Studio Notebook은 Monaco 편집기와 통합되어 IDE 스타일 IntelliSense를 셀 편집기로 가져옵니다. 구문 강조 표시, 오류 표식 및 자동 코드 완성을 통해 코드를 작성 하 고 문제를 더 빠르게 식별할 수 있습니다.

IntelliSense 기능은 완성도 수준이 언어마다 다릅니다. 다음 표를 사용 하 여 지원 되는 항목을 확인 합니다.

|언어| 구문 강조 표시 | 구문 오류 표식  | 구문 코드 완성 | 변수 코드 완성| 시스템 함수 코드 완성| 사용자 함수 코드 완성| 스마트 들여쓰기 | 코드 접기|
|--|--|--|--|--|--|--|--|--|
|PySpark(Python)|예|예|예|예|예|예|예|예|
|Spark(Scala)|예|예|예|예|-|-|-|예|
|SparkSQL|예|예|-|-|-|-|-|-|
|.NET for Spark(C#)|예|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>도구 모음 단추를 사용하여 텍스트 셀 서식 지정

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

텍스트 셀 도구 모음의 서식 단추를 사용하여 일반적인 Markdown 작업을 수행할 수 있습니다. 여기에는 텍스트 굵게 표시, 텍스트에 기울임꼴 적용, 코드 조각 삽입, 순서가 지정되지 않은 목록 삽입, 순서가 지정된 목록 삽입, URL의 이미지 삽입이 포함됩니다.

  ![Synapse 텍스트 셀 도구 모음](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

서식 단추 도구 모음은 아직 미리 보기 노트북 환경에서 사용할 수 없습니다. 

---

### <a name="undo-cell-operations"></a>셀 작업 실행 취소

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

**실행 취소** 단추를 선택 하거나 **ctrl + Z** 를 눌러 가장 최근 셀 작업을 취소 합니다. 이제 기록된 최근 셀 작업을 20개까지 실행 취소할 수 있습니다. 

   ![Synapse 실행 취소 셀](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

Preview preview 작업은 아직 preview 노트북 환경에서 사용할 수 없습니다. 

---

### <a name="move-a-cell"></a>셀 이동

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

줄임표(...)를 선택하면 오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스할 수 있습니다. 그런 다음, **위로 셀 이동** 또는 **아래로 셀 이동** 을 선택하여 현재 셀을 이동합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 셀을 위로 이동하려면 **Ctrl+Alt+↑** 키를 누릅니다. 현재 셀을 아래로 이동하려면 **Ctrl+Alt+↓** 키를 누릅니다.

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

셀의 왼쪽을 클릭 하 고 원하는 위치로 끕니다. 
    ![Synapse 셀 이동](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>셀 삭제

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

셀을 삭제하려면 줄임표(...)를 선택하여 오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스한 후 **셀 삭제** 를 선택합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. 현재 행을 삭제하려면 **D,D** 를 누릅니다.
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

셀을 삭제 하려면 셀의 오른쪽에 있는 삭제 단추를 선택 합니다. 

[명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용할 수도 있습니다. **Shift + D** 를 눌러 현재 셀을 삭제 합니다. 

   ![azure-노트북-셀 삭제](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>셀 입력 접기

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

현재 셀의 아래쪽에 있는 화살표 단추를 선택 하 여 축소 합니다. 확장 하려면 셀이 축소 된 상태에서 화살표 단추를 선택 합니다.

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

셀 도구 모음에서 **더 많은 명령** 줄임표 (...)를 선택 하 고 현재 셀의 입력을 축소 하려면 **입력** 합니다. 확장 하려면 셀을 축소 하는 동안 **숨겨진 입력** 을 선택 합니다.

   ![azure-노트북-축소-셀 입력](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>셀 출력 접기

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

현재 셀 출력의 왼쪽 위에 있는 **출력 축소** 단추를 선택 하 여 축소 합니다. 확장 하려면 셀 출력이 축소 된 상태에서 **셀 출력 표시** 를 선택 합니다.

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

셀 도구 모음에서 **더 많은 명령** 줄임표 (...)를 선택 하 고 현재 셀의 출력을 축소 하려면 **출력** 을 선택 합니다. 확장 하려면 셀의 출력이 숨겨지는 동안 동일한 단추를 선택 합니다.

   ![azure-노트북-셀 축소-출력](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Notebook 실행

Notebook의 코드 셀을 개별적으로 또는 한꺼번에 실행할 수 있습니다. 각 셀의 상태와 진행률이 Notebook에 표시됩니다.

### <a name="run-a-cell"></a>셀 실행

셀에서 코드를 실행하는 방법은 여러 가지입니다.

1. 실행할 셀을 마우스로 가리킨 다음, **셀 실행** 단추를 선택하거나 **Ctrl+Enter** 키를 누릅니다.

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. [명령 모드에서 바로 가기 키](#shortcut-keys-under-command-mode)를 사용합니다. **Shift+Enter** 키를 눌러서 현재 셀을 실행하고 아래 셀을 선택합니다. **Alt+Enter** 키를 눌러 현재 셀을 실행하고 아래에 새 셀을 삽입합니다.

---

### <a name="run-all-cells"></a>모든 셀 실행
**모두 실행** 단추를 선택 하 여 현재 노트북의 모든 셀을 순서 대로 실행 합니다.

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>위 또는 아래 셀 모두 실행

오른쪽 끝에 있는 추가 셀 작업 메뉴에 액세스하려면 줄임표( **...** )를 선택합니다. 그런 다음, 현재 셀 위에 있는 모든 셀을 순서대로 실행하려면 **Run cells above**(위 셀 실행)를 선택합니다. 현재 셀 아래 모든 셀을 순서대로 실행하려면 **Run cells below**(아래 셀 실행)을 선택합니다.

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>실행 중인 모든 셀 취소
실행 중인 셀 또는 큐에서 대기 중인 셀을 취소 하려면 **모두 취소** 단추를 선택 합니다. 
   ![모두 취소-셀](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

아직 실행 되지 않은 모든 셀은 preview 노트북 환경에서 사용할 수 없습니다. 

---



### <a name="reference-notebook"></a>참조 노트북

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

지원 안 됨

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

```%run <notebook path>```매직 명령을 사용 하 여 현재 노트북의 컨텍스트 내에서 다른 노트북을 참조할 수 있습니다. 참조 노트북에 정의 된 모든 변수는 현재 노트북에서 사용할 수 있습니다. ```%run``` 매직 명령은 중첩 된 호출을 지원 하지만 재귀 호출을 지원 하지 않습니다. 문 깊이가 5 보다 크면 예외를 수신 합니다. ```%run``` 현재 명령은 전자 필기장 경로를 매개 변수로 전달 하는 기능만 지원 합니다. 

---


### <a name="cell-status-indicator"></a>셀 상태 표시기

셀 아래에 단계별 셀 실행 상태가 표시되어 현재 진행 상황을 확인할 수 있습니다. 셀 실행이 완료되면 총 지속 시간 및 종료 시간이 포함된 실행 요약이 표시되고 나중에 참조할 수 있도록 보관됩니다.

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 진행률 표시기

Azure Synapse Studio Notebook은 순전히 Spark 기반입니다. 코드 셀은 서버를 사용 하지 않는 Apache Spark 풀에서 원격으로 실행 됩니다. Spark 작업 진행률 표시기에 실시간 진행률 표시줄이 제공되어 작업 실행 상태를 이해하는 데 유용합니다.
각 작업 또는 단계 당 태스크 수를 통해 spark 작업의 병렬 수준을 식별할 수 있습니다. 작업 (또는 단계) 이름에 대 한 링크를 선택 하 여 특정 작업 (또는 단계)의 Spark UI를 자세히 살펴볼 수도 있습니다.


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 세션 구성

**세션 구성** 에서 현재 Spark 세션에 제공할 실행기의 크기와 수, 제한 시간을 지정할 수 있습니다. Spark 세션을 다시 시작하면 구성 변경 사항이 적용됩니다. 캐시된 Notebook 변수는 모두 지워집니다.

[![세션 관리](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Spark 세션 구성 매직 명령
매직 명령 **%% configure** 를 통해 spark 세션 설정을 지정할 수도 있습니다. 설정이 적용 되도록 spark 세션을 다시 시작 해야 합니다. 노트북의 시작 부분에서 **%% configure** 를 실행 하는 것이 좋습니다. 다음은 샘플입니다. https://github.com/cloudera/livy#request-body 유효한 매개 변수의 전체 목록은를 참조 하세요. 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```


## <a name="bring-data-to-a-notebook"></a>Notebook에 데이터 가져오기

아래 코드 샘플과 같이 Azure Blob Storage, Azure Data Lake Store Gen 2 및 SQL 풀에서 데이터를 로드할 수 있습니다.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Azure Data Lake Store Gen2에서 Spark DataFrame으로 CSV 읽기

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Azure Blob Storage에서 Spark DataFrame으로 CSV 읽기

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>기본 스토리지 계정에서 데이터 읽기

기본 스토리지 계정의 데이터에 직접 액세스할 수 있습니다. 비밀 키를 제공할 필요가 없습니다. 데이터 탐색기에서 파일을 마우스 오른쪽 단추로 클릭하고 **새 Notebook** 을 선택하여 데이터 추출기가 자동으로 생성된 새 Notebook을 확인합니다.

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Notebook 저장

작업 영역에 단일 Notebook 또는 모든 Notebook을 저장할 수 있습니다.

1. 단일 Notebook에 변경한 내용을 저장하려면 노트북 명령 모음에서 **게시** 단추를 선택합니다.

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 작업 영역에 모든 노트북을 저장하려면 작업 영역 명령 모음에서 **모두 게시** 단추를 선택합니다. 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

Notebook 속성에서 저장할 때 셀 출력을 포함할지 여부를 구성할 수 있습니다.

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>매직 명령
Azure Synapse Studio 노트북에서 친숙 한 Jupyter 매직 명령을 사용할 수 있습니다. 현재 사용 가능한 매직 명령으로 다음 목록을 검토 합니다. 사용자의 요구를 충족 하기 위해 더 많은 매직 명령을 계속 빌드할 수 있도록 [GitHub에서 사용 사례](https://github.com/MicrosoftDocs/azure-docs/issues/new) 를 알려 주세요.

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

사용 가능한 줄 매직: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

사용 가능한 셀 매직: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% sql](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% spark](#use-multiple-languages), [% csharp](#use-multiple-languages),[%% configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

사용 가능한 줄 매직: [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic),% [time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time),% [timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [% history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [% run](#reference-notebook), [% load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

사용 가능한 셀 매직: [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%% timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%% capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%% writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%% sql](#use-multiple-languages), [%% pyspark](#use-multiple-languages), [%% spark](#use-multiple-languages), [% csharp](#use-multiple-languages),% [% html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%% configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>노트북 통합

### <a name="add-a-notebook-to-a-pipeline"></a>파이프라인에 노트북 추가

오른쪽 위 모서리에서 **파이프라인에 추가** 단추를 선택 하 여 기존 파이프라인에 노트북을 추가 하거나 새 파이프라인을 만듭니다.

![파이프라인에 노트북 추가](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>매개 변수 지정 셀

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

노트북을 매개 변수화 하려면 맨 오른쪽에 있는 추가 셀 작업 메뉴에 액세스 하려면 줄임표 (...)를 선택 합니다. 그런 다음 매개 변수 셀 **설정/해제** 를 선택 하 여 셀을 매개 변수 셀로 지정 합니다.

![토글 매개 변수](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

노트북을 매개 변수화 하려면 줄임표 (...)를 선택 하 여 셀 도구 모음에서 **더 많은 명령** 에 액세스 합니다. 그런 다음 매개 변수 셀 **설정/해제** 를 선택 하 여 셀을 매개 변수 셀로 지정 합니다.

![azure-노트북-전환-매개 변수](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory는 매개 변수 셀을 찾고 실행 시 전달 되는 매개 변수의 기본값으로이 셀을 처리 합니다. 실행 엔진이 기본값을 덮어쓰기 위해 입력 매개 변수를 사용 하 여 매개 변수 셀 아래에 새 셀을 추가 합니다. 매개 변수 셀이 지정 되지 않은 경우 삽입 된 셀이 노트북의 맨 위에 삽입 됩니다.


### <a name="assign-parameters-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수가 포함 된 노트북을 만든 후에는 Azure Synapse 노트북 활동을 사용 하 여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가한 후에는 **설정** 탭의 **기본 매개 변수** 섹션에서 매개 변수 값을 설정할 수 있습니다. 

![매개 변수 할당](./media/apache-spark-development-using-notebooks/assign-parameter.png)

매개 변수 값을 할당 하는 경우 [파이프라인 식 언어](../../data-factory/control-flow-expression-language-functions.md) 또는 [시스템 변수](../../data-factory/control-flow-system-variables.md)를 사용할 수 있습니다.



## <a name="shortcut-keys"></a>바로 가기 키

Jupyter 노트북과 마찬가지로 Azure Synapse Studio Notebook에는 모달 사용자 인터페이스가 있습니다. 키보드는 Notebook 셀이 있는 모드에 따라 다른 작업을 수행합니다. Synapse Studio Notebook은 지정된 코드 셀에 대해 두 가지 모드(명령 모드 및 편집 모드)를 지원합니다.

1. 입력하라는 텍스트 커서가 없으면 셀은 명령 모드에 있습니다. 셀이 명령 모드에 있으면 Notebook을 전체적으로 편집할 수 있지만 개별 셀에는 입력할 수 없습니다. `ESC`마우스를 사용 하거나 마우스를 사용 하 여 셀의 편집기 영역 밖에 서 선택 하 여 명령 모드를 시작 합니다.

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. 편집 모드에서는 편집기 영역에 입력하라는 텍스트 커서가 표시됩니다. 셀이 편집 모드에 있는 경우 셀에 입력할 수 있습니다. 마우스를 누르거나 마우스를 `Enter` 사용 하 여 셀의 편집기 영역을 선택 하 여 편집 모드를 시작 합니다.
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>명령 모드의 바로 가기 키

# <a name="classical-notebook"></a>[클래식 노트북](#tab/classical)

다음과 같은 키 입력 바로 가기를 사용하면 Azure Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Studio Notebook 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래 선택 | Shift+Enter |
|현재 셀을 실행하고 아래에 삽입 | Alt+Enter |
|위 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|선택한 셀을 위로 확장| Shift+위쪽 화살표 |
|선택한 셀을 아래로 확장| Shift+아래쪽 화살표|
|위로 셀 이동| Ctrl+Alt+↑ |
|아래로 셀 이동| Ctrl+Alt+↓ |
|선택한 셀 삭제| D, D |
|편집 모드로 전환| Enter |

# <a name="preview-notebook"></a>[노트북 미리 보기](#tab/preview)

| 작업 |Synapse Studio Notebook 바로 가기  |
|--|--|
|현재 셀을 실행하고 아래 선택 | Shift+Enter |
|현재 셀을 실행하고 아래에 삽입 | Alt+Enter |
|현재 셀 실행| Ctrl+Enter |
|위 셀 선택| 위로 |
|아래 셀 선택| 아래로 |
|이전 셀 선택| K |
|다음 셀 선택| J |
|위에 셀 삽입| A |
|아래에 셀 삽입| b |
|선택한 셀 삭제| Shift + D |
|편집 모드로 전환| Enter |

---

### <a name="shortcut-keys-under-edit-mode"></a>편집 모드의 바로 가기 키


편집 모드에서 다음과 같은 키 입력 바로 가기를 사용하면 Azure Synapse Notebook에서 보다 편리하게 코드를 탐색하고 실행할 수 있습니다.

| 작업 |Synapse Studio Notebook 바로 가기  |
|--|--|
|커서를 위로 이동 | 위로 |
|커서를 아래로 이동|아래로|
|실행 취소|Ctrl + Z|
|다시 실행|Ctrl + Y|
|주석 처리/주석 처리 제거|Ctrl + /|
|이전 단어 삭제|Ctrl + Backspace|
|다음 단어 삭제|Ctrl + Delete|
|셀 시작으로 이동|Ctrl + Home|
|셀 끝으로 이동 |Ctrl + End|
|한 단어 왼쪽으로 이동|Ctrl + Left|
|한 단어 오른쪽으로 이동|Ctrl + 오른쪽 화살표|
|모두 선택|Ctrl + A|
|들여쓰기| Ctrl +]|
|내어쓰기|Ctrl + [|
|명령 모드로 전환| Esc |

---

## <a name="next-steps"></a>다음 단계
- [Synapse 샘플 노트북 확인](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [빠른 시작: 웹 도구를 사용하여 Azure Synapse Analytics에서 Apache Spark 풀 만들기](../quickstart-apache-spark-notebook.md)
- [Azure Synapse Analytics의 Apache Spark란?](apache-spark-overview.md)
- [Azure Synapse Analytics를 사용하여 Apache Spark에 .NET 사용](spark-dotnet.md)
- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
