---
title: '텍스트 전처리: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 전처리 텍스트 모듈을 사용 하 여 텍스트를 정리 하 고 간소화 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659310"
---
# <a name="preprocess-text"></a>텍스트 전처리

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

텍스트 **전처리** 모듈을 사용 하 여 텍스트를 정리 하 고 간소화할 수 있습니다. 이러한 일반 텍스트 처리 작업을 지원 합니다.

* 중지 단어 제거
* 정규식을 사용 하 여 특정 대상 문자열 검색 및 바꾸기
* 분류 정리-여러 개의 관련 단어를 단일 정규 형식으로 변환 합니다.
* 대/소문자 정규화
* 숫자, 특수 문자, 반복 되는 문자 시퀀스 (예: "aaaa") 등의 특정 문자 클래스 제거
* 전자 메일 및 Url의 식별 및 제거

**전처리 텍스트** 모듈은 현재 영어만 지원 합니다.

## <a name="configure-text-preprocessing"></a>텍스트 전처리 구성  

1.  Azure Machine Learning에서 파이프라인에 **전처리 텍스트** 모듈을 추가 합니다. 이 모듈은 **Text Analytics** 에서 찾을 수 있습니다.

1. 텍스트를 포함 하는 열이 하나 이상 있는 데이터 집합을 연결 합니다.

1. **언어** 드롭다운 목록에서 언어를 선택 합니다.

1. **정리할 텍스트 열**: 전처리 하려는 열을 선택 합니다.

1. **중지 단어 제거**: 미리 정의 된 중지 단어 목록을 텍스트 열에 적용 하려면이 옵션을 선택 합니다. 

    중지 단어 목록은 언어에 따라 다르며 사용자 지정할 수 있습니다.

1. **분류 정리**: 정규 형식으로 단어를 표시 하려면이 옵션을 선택 합니다. 이 옵션은 다른 경우와 유사한 텍스트 토큰의 고유한 발생 수를 줄이는 데 유용 합니다.

    분류 정리 프로세스는 언어에 따라 달라 집니다.

1. **문장 검색**: 분석을 수행할 때 모듈에서 문장 경계 표시를 삽입 하도록 하려면이 옵션을 선택 합니다.

    이 모듈에서는 일련의 3 개의 파이프 문자를 사용 하 여 `|||` 문장 종결자를 나타냅니다.

1. 정규식을 사용 하 여 선택적 찾기 및 바꾸기 작업을 수행 합니다. 정규식은 다른 모든 기본 제공 옵션 보다 먼저 처리 됩니다.

    * **사용자 지정 정규식**: 검색 하는 텍스트를 정의 합니다.
    * **사용자 지정 대체 문자열**: 단일 대체 값을 정의 합니다.

1. 소문자 **를 소문자로 정규화**: ASCII 대문자를 소문자 형식으로 변환 하려면이 옵션을 선택 합니다.

    문자가 정규화 되지 않은 경우 대문자 및 소문자의 동일한 단어는 두 단어로 간주 됩니다.

1. 처리 된 출력 텍스트에서 다음과 같은 유형의 문자 또는 문자 시퀀스를 제거할 수도 있습니다.

    * **숫자 제거**: 지정 된 언어의 모든 숫자 문자를 제거 하려면이 옵션을 선택 합니다. Id 번호는 도메인 종속 및 언어에 따라 달라 집니다. 숫자 문자가 알려진 단어의 정수 부분이 면 숫자가 제거 되지 않을 수 있습니다. [기술 참고 사항](#technical-notes)을 자세히 알아보세요.
    
    * **특수 문자 제거**: 영숫자가 아닌 특수 문자를 모두 제거 하려면이 옵션을 사용 합니다.
    
    * **중복 문자 제거**: 두 번 이상 반복 되는 시퀀스에서 추가 문자를 제거 하려면이 옵션을 선택 합니다. 예를 들어 "aaaaa"와 같은 시퀀스는 "aa"로 줄어듭니다.
    
    * **전자 메일 주소 제거**: 형식의 시퀀스를 제거 하려면이 옵션을 선택 `<string>@<string>` 합니다.  
    * Url **제거**: 다음 url 접두사가 포함 된 시퀀스를 제거 하려면이 옵션을 선택 합니다.,, `http` `https` `ftp` ,`www`
    
1. **Expand verb 축약**:이 옵션은 verb 축약를 사용 하는 언어에만 적용 됩니다. 현재는 영어로만 되어 있습니다. 

    예를 들어이 옵션을 선택 하면 "유지 되지 않습니다." *라는 문구를* *"유지 하지 않습니다*"로 바꿀 수 있습니다.

1. **백슬래시로 백슬래시 정규화**:의 모든 인스턴스를에 매핑하려면이 옵션을 `\\` 선택 `/` 합니다.

1. **특수 문자에 대 한 토큰 분할**:, 등의 문자에 대 한 단어를 중단 하려면이 옵션을 선택 `&` `-` 합니다. 이 옵션은 또한 두 번 이상 반복 될 때 특수 문자를 줄일 수 있습니다. 

    예를 들어 문자열은, `MS---WORD` 및의 세 가지 토큰으로 구분 됩니다 `MS` `-` `WORD` .

1. 파이프라인을 제출합니다.

## <a name="technical-notes"></a>기술 정보

Studio (클래식) 및 디자이너의 **전처리 텍스트** 모듈은 다양 한 언어 모델을 사용 합니다. 이 디자이너는 [spaCy](https://spacy.io/models/en)에서 다중 작업 cnn 학습 된 모델을 사용 합니다. 다른 모델에서는 서로 다른 결과를 초래 하는 비 토크 및 음성 부분 태거를 제공 합니다.

다음은 몇 가지 예제입니다.

| Configuration | 출력 결과 |
| --- | --- |
|모든 옵션 선택 </br> 보고 </br> ' WC.EXE-3 3test 4test '의 ' 3test '와 같은 경우 디자이너는 전체 단어 ' 3test '를 제거 합니다 .이 컨텍스트에서는 음성 부분 태거는이 토큰 ' 3test '를 숫자로 지정 하 고 음성 부분에 따라 모듈을 제거 합니다.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="모든 옵션 선택" border="True"::: |
|선택한 항목만 `Removing number` </br> 보고 </br> ' 3test ', ' 4-EC ' 등의 경우 디자이너 토크 백신는 이러한 경우를 분할 하지 않고 전체 토큰으로 처리 합니다. 따라서 이러한 단어에 있는 숫자는 제거 되지 않습니다.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="' 제거 번호 '만 선택 합니다." border="True"::: |

정규식을 사용 하 여 사용자 지정 된 결과를 출력할 수도 있습니다.

| Configuration | 출력 결과 |
| --- | --- |
|모든 옵션 선택 </br> 사용자 지정 정규식: `(\s+)*(-|\d+)(\s+)*` </br> 사용자 지정 대체 문자열: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="모든 옵션이 선택 되 고 정규식 사용" border="True"::: |
|선택한 항목만 `Removing number` </br> 사용자 지정 정규식: `(\s+)*(-|\d+)(\s+)*` </br> 사용자 지정 대체 문자열: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="선택한 숫자 제거 및 정규식" border="True"::: |


## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 