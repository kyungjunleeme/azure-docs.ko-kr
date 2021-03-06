---
title: Azure HDInsight 릴리스 정보
description: Azure HDInsight에 대한 최신 릴리스 정보입니다. Hadoop, Spark, R Server, Hive 등에 대한 개발 팁 및 세부 정보를 확인하세요.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 0895e84363d40bdbf30408f2b2a0d95f951eb303
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032561"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 릴리스 정보

이 문서에서는 **가장 최근** Azure HDInsight 릴리스 업데이트에 대한 정보를 제공합니다. 이전 릴리스에 대한 자세한 내용은 [HDInsight 릴리스 정보 보관](hdinsight-release-notes-archive.md)을 참조하세요.

## <a name="summary"></a>요약

Azure HDInsight는 Azure에서 오픈 소스 분석을 위해 기업 고객들 사이에서 가장 인기 있는 서비스 중 하나입니다.

릴리스 정보를 구독 하려는 경우 [이 GitHub 리포지토리에서](https://github.com/hdinsight/release-notes/releases)릴리스를 시청 하세요.

## <a name="release-date-11182020"></a>릴리스 날짜: 11/18/2020

이 릴리스는 HDInsight 3.6 및 HDInsight 4.0 모두에 적용 됩니다. HDInsight 릴리스는 며칠 동안의 준비 작업을 거쳐 모든 지역에서 사용할 수 있게 됩니다. 여기에 나오는 릴리스 날짜는 첫 번째 지역 릴리스 날짜를 나타냅니다. 아래 변경 내용이 표시 되지 않으면 며칠 동안 해당 지역에서 릴리스가 라이브 될 때까지 기다립니다.

## <a name="new-features"></a>새 기능
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>미사용 고객 관리 키 암호화에 대 한 자동 키 회전
이 릴리스부터는 고객이 휴지 상태의 고객이 관리 하는 키 암호화를 위해 Azure KeyValut 버전 감소 암호화 키 Url을 사용할 수 있습니다. HDInsight는 키가 만료 되거나 새 버전으로 바뀔 때 자동으로 키를 회전 합니다. 자세한 내용은 [여기](./disk-encryption.md)를 참조하세요.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Spark, Hadoop 및 ML 서비스에 대해 다른 사육 사 가상 머신 크기를 선택 하는 기능
이전에 HDInsight는 Spark, Hadoop 및 ML 서비스 클러스터 유형에 대 한 아웃 지원팀 노드 크기 사용자 지정을 지원 하지 않았습니다. 기본적으로 A2_v2/A2 가상 머신 크기가 무료로 제공 됩니다. 이 릴리스에서는 시나리오에 가장 적합 한 사육 사 가상 머신 크기를 선택할 수 있습니다. A2_v2/A2 이외의 가상 컴퓨터 크기를 포함 하는 사육 아웃 노드에는 요금이 부과 됩니다. A2_v2 및 A2 가상 머신은 계속 무료로 제공 됩니다.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets로 전환
이제 HDInsight는 Azure Virtual Machines를 사용하여 클러스터를 프로비저닝합니다. 이 버전부터 서비스는 [Azure 가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)으로 점진적으로 마이그레이션됩니다. 전체 프로세스에는 몇 개월이 걸릴 수 있습니다. 지역 및 구독이 마이그레이션된 후에는 새로 만든 HDInsight 클러스터가 고객의 작업 없이도 가상 머신 확장 집합에서 실행 됩니다. 주요 변경 내용은 필요 하지 않습니다.

## <a name="deprecation"></a>사용 중단
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>HDInsight 3.6 ML 서비스 클러스터의 사용 중단
HDInsight 3.6 ML Services 클러스터 유형은 12 월 31 2020 일까 지 지원 될 예정입니다. 고객은 12 월 31 2020 일 이후 새로운 3.6 ML 서비스 클러스터를 만들지 않습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. [여기](./hdinsight-component-versioning.md#available-versions)에서 HDInsight 버전 및 클러스터 유형에 대 한 지원 만료를 확인 합니다.

### <a name="disabled-vm-sizes"></a>비활성화 된 VM 크기
11 월 16 2020부터 HDInsight는 standand_A8, standand_A9, standand_A10 및 standand_A11 VM 크기를 사용 하 여 클러스터를 만드는 새 고객을 차단 합니다. 지난 3 달 동안 이러한 VM 크기를 사용한 기존 고객에 게는 영향을 주지 않습니다. 9 2021 년 1 월 1 일부 터 HDInsight는 standand_A8, standand_A9, standand_A10 및 standand_A11 VM 크기를 사용 하 여 클러스터를 만드는 모든 고객을 차단 합니다. 기존 클러스터는 그대로 실행 됩니다. 잠재적인 시스템/지원 중단을 방지 하려면 HDInsight 4.0로 이동 하는 것이 좋습니다.

## <a name="behavior-changes"></a>동작 변경
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>크기 조정 작업 전에 NSG 규칙 검사 추가
HDInsight는 크기 조정 작업을 통해 NSGs (네트워크 보안 그룹) 및 UDRs (사용자 정의 경로) 검사를 추가 했습니다. 클러스터를 만들 뿐만 아니라 클러스터 크기 조정을 위해 동일한 유효성 검사가 수행 됩니다. 이 유효성 검사는 예기치 않은 오류를 방지 하는 데 도움이 됩니다. 유효성 검사를 통과 하지 못하면 크기 조정이 실패 합니다. NSGs 및 UDRs를 올바르게 구성 하는 방법에 대 한 자세한 내용은 [HDInsight 관리 IP 주소](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)를 참조 하세요.

## <a name="upcoming-changes"></a>예정된 변경
이후 릴리스에서는 다음과 같은 변경이 수행됩니다.

### <a name="default-cluster-version-will-be-changed-to-40"></a>기본 클러스터 버전이 4.0로 변경 됩니다.
2021 2 월부터 HDInsight 클러스터의 기본 버전이 3.6에서 4.0로 변경 됩니다. 사용 가능한 버전에 대 한 자세한 내용은 [사용 가능한 버전](./hdinsight-component-versioning.md#available-versions)을 참조 하세요. [HDInsight 4.0](./hdinsight-version-release.md) 의 새로운 기능에 대 한 자세한 정보

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3.6 6 월 30 2021의 지원 종료
HDInsight 3.6은 지원 종료 될 예정입니다. 6 월 30 2021 양식을 시작 하면 고객이 새 HDInsight 3.6 클러스터를 만들 수 없습니다. 기존 클러스터는 Microsoft의 지원 없이 있는 그대로 실행됩니다. 잠재적인 시스템/지원 중단을 방지 하려면 HDInsight 4.0로 이동 하는 것이 좋습니다.

## <a name="bug-fixes"></a>버그 수정
HDInsight는 계속해서 클러스터 안정성과 성능을 향상시킵니다. 

## <a name="component-version-change"></a>구성 요소 버전 변경
이 릴리스에 대한 구성 요소 버전이 변경되지 않았습니다. [이 문서](./hdinsight-component-versioning.md)에서 hdinsight 4.0 및 hdinsight 3.6의 최신 구성 요소 버전을 찾을 수 있습니다.

## <a name="known-issues"></a>알려진 문제
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>HDInsight 클러스터 Vm이 주기적으로 재부팅 되지 않도록 방지

2020 년 11 월부터 시작 하 여 HDInsight 클러스터 Vm을 정기적으로 다시 부팅 하는 것을 알 수 있습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

1.  Clamav는 클러스터에서 사용 하도록 설정 됩니다. 새 azsec-clamav 패키지는 노드 다시 부팅을 트리거하는 많은 양의 메모리를 사용 합니다. 
2.  CRON 작업은 Azure 서비스에서 사용 하는 Ca (인증 기관)의 목록에 대 한 변경 내용을 모니터링 하는 매일 예약 됩니다. 새 CA 인증서를 사용할 수 있는 경우이 스크립트는 인증서를 JDK 신뢰 저장소에 추가 하 고 재부팅을 예약 합니다.

HDInsight는 실행 중인 모든 클러스터에 대해 수정 사항을 배포 하 고 패치를 적용 하 여 두 문제를 해결 합니다. 수정 사항을 즉시 적용 하 고 예기치 않은 Vm 재부팅을 방지 하려면 모든 클러스터 노드에서 다음 스크립트 작업을 영구 스크립트 작업으로 실행할 수 있습니다. 수정 및 패치 완료 후 HDInsight에서 다른 알림을 게시 합니다.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```

