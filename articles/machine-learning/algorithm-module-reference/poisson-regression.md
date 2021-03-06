---
title: '포아송 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 포아송 회귀 모듈을 사용 하 여 포아송 회귀 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420702"
---
# <a name="poisson-regression"></a>포아송 회귀

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

이 모듈을 사용 하 여 파이프라인에서 포아송 회귀 모델을 만들 수 있습니다. 포아송 회귀는 숫자 값을 예측 하기 위한 것 이며 일반적으로 개수입니다. 따라서 예측 하려는 값이 다음 조건에 부합 하는 경우에만이 모듈을 사용 하 여 회귀 모델을 만들어야 합니다.

- 응답 변수에는 [포아송 분포가](https://en.wikipedia.org/wiki/Poisson_distribution)있습니다.  

- 개수는 음수일 수 없습니다. 음의 레이블과 함께 사용하려고 하면 이 방법이 완전히 실패합니다.

- 포아송 분포는 불연속 분포입니다. 따라서 정수가 아닌 숫자를 사용 하 여이 메서드를 사용 하는 것은 의미가 없습니다.

> [!TIP]
> 대상이 개수가 아니면 포아송 회귀는 적절한 방법이 아닙니다. [디자이너에서 다른 회귀 모듈을](./module-reference.md#machine-learning-algorithms)사용해 봅니다. 

회귀 메서드를 설정한 후에는 예측 하려는 값의 예제가 포함 된 데이터 집합을 사용 하 여 모델을 학습 해야 합니다. 그러면 학습된 모델을 예측에 사용할 수 있습니다.

## <a name="more-about-poisson-regression"></a>포아송 회귀에 대 한 자세한 정보

포아송 회귀는 보통 개수를 모델링하는 데 사용되는 특수 회귀 분석 유형입니다. 예를 들어 다음과 같은 시나리오에서 포아송 회귀를 사용하면 유용합니다.

- 항공편과 관련된 콜드 횟수 모델링

- 이벤트 중에 응급 서비스 호출 수 예측

- 프로 모션 이후 고객 문의 수 예측

- 대체 테이블 만들기

응답 변수에는 포아송 분포가 있으므로 모델은 가장 정사각형이 아닌 회귀와 같이 데이터 및 확률 분포에 대해 서로 다른 가정을 합니다. 따라서 포아송 모델은 다른 회귀 모델과 다르게 해석 되어야 합니다.

## <a name="how-to-configure-poisson-regression"></a>포아송 회귀를 구성 하는 방법

1. 디자이너에서 **포아송 회귀** 모듈을 파이프라인에 추가 합니다. **회귀** 범주의 **Machine Learning 알고리즘** 에서이 모듈을 찾을 수 있습니다.

2. 올바른 유형의 학습 데이터를 포함 하는 데이터 집합을 추가 합니다. 

    회귀 변수를 학습 하는 데 사용 하기 전에 [데이터 정규화](normalize-data.md) 를 사용 하 여 입력 데이터 집합을 정규화 하는 것이 좋습니다.

3. **포아송 회귀** 모듈의 오른쪽 창에서 **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.  
  
    - **단일 매개 변수** : 모델을 구성 하는 방법을 아는 경우 특정 값 집합을 인수로 제공 합니다.
  
    - **매개 변수 범위** : 가장 적합 한 매개 변수를 잘 모르는 경우 [Model hyperparameters 조정](tune-model-hyperparameters.md) 모듈을 사용 하 여 매개 변수 스윕을 수행 합니다. 강사는 최적의 구성을 찾기 위해 지정 하는 여러 값을 반복 합니다.
  
4. **최적화 허용 오차** : 최적화 중 허용 시간 간격을 정의 하는 값을 입력 합니다. 값이 작을수록는 속도는 느려지고 맞춤은 더 정확해집니다.

5. **L1 정규화 weight** 및 **l2 정규화 가중치** : l1 및 l2 정규화에 사용할 형식 값입니다. *정규화* 를 통해 학습 데이터와 독립적인 모델의 요소에 관한 알고리즘에 제약 조건을 추가합니다. 과잉 맞춤을 방지하려는 경우에 일반적으로 정규화를 사용합니다. 

    - 모델의 스파스 수준을 최대화하려는 경우에는 L1 정규화가 유용합니다.

        학습자가 최소화하려는 손실 식에서 가중치 벡터의 L1 가중치를 빼는 방식으로 L1 정규화를 수행합니다. L1 표준은 0이 아닌 좌표의 수인 L0 표준에 대한 적절한 근사치입니다.

    - L2 정규화를 사용하면 가중치 벡터에 있는 단일 좌표의 크기가 너무 커지지 않게 합니다. L2 정규화는 전체 가중치가 작은 모델을 목표로 하는 경우 유용합니다.

    이 모듈에서는 L1 및 L2 정규화 조합을 적용할 수 있습니다. L1 및 L2 정규화를 결합 하 여 매개 변수 값의 크기에 대 한 페널티를 적용할 수 있습니다. 학습자는 페널티를 최소화하려고 하며, 이 과정에서 손실도 최소화됩니다.

    L1 및 L2 정규화에 대 한 자세한 내용은 [Machine Learning에 대 한 l1 및 L2 정규화](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)를 참조 하세요.

6. **BFGS에 대 한 메모리 크기** : 모델 맞춤 및 최적화를 위해 예약할 메모리 양을 지정 합니다.

     BFGS는 Broyden – Fletcher – Goldfarb – Shanno (BFGS) 알고리즘을 기반으로 하는 최적화의 특정 메서드입니다. 메서드는 제한 된 양의 메모리 (L)를 사용 하 여 다음 단계 방향을 계산 합니다.

     이 매개 변수를 변경 하면 다음 단계를 계산 하기 위해 저장 되는 과거 위치 및 그라데이션의 수에 영향을 줄 수 있습니다.

7. 학습 모듈 중 하나에 학습 데이터 집합 및 학습 되지 않은 모델을 연결 합니다. 

    - 담당자 **모드 만들기** 를 **단일 매개 변수로** 설정한 경우 [모델 학습](train-model.md) 모듈을 사용 합니다.

    - **만든이 모드** 를 **매개 변수 범위** 로 설정 하는 경우 [모델 hyperparameters 변수 조정](tune-model-hyperparameters.md) 모듈을 사용 합니다.

    > [!WARNING]
    > 
    > - [모델 학습](train-model.md)에 매개 변수 범위를 전달 하는 경우 매개 변수 범위 목록의 첫 번째 값만 사용 합니다.
    > 
    > - 단일 매개 변수 값 집합을 [모델 하이퍼 매개 변수 조정](tune-model-hyperparameters.md) 모듈에 전달 하는 경우 각 매개 변수에 대 한 설정 범위가 필요한 경우 값을 무시 하 고 학습자에 대 한 기본값을 사용 합니다.
    > 
    > - **매개 변수 범위** 옵션을 선택 하 고 매개 변수에 대해 단일 값을 입력 하는 경우 다른 매개 변수가 값 범위에서 변경 되더라도 지정한 단일 값은 스윕 전체에서 사용 됩니다.

8.  파이프라인을 제출합니다.

## <a name="results"></a>결과

학습 완료 후:

+ 학습 된 모델의 스냅숏을 저장 하려면 학습 모듈을 선택한 다음 오른쪽 패널에서 **출력 + 로그** 탭으로 전환 합니다. 아이콘 **등록 데이터 집합** 을 클릭 합니다.  모듈 트리에서 저장 된 모델을 모듈로 찾을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요.