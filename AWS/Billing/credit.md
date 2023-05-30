# AWS 크레딧

AWS 크레딧은 적격 서비스와 연결된 비용을 부담하기 위해 요금에 자동으로 적용할 수 있는 크레딧입니다. 


1. 적용할 크레딧 선택

    1. 크레딧 중에서 가장 먼저 만료되는 순서
    2. 적격 서비스 수가 가장 적은 크레딧
    3. 가장 오래된 크레딧


2. 크레딧을 적용할 대상 선택
> 크레딧 공유가 활성화되었을 때 AWS Organizations에서 크레딧이 적용되는 순서

    1. 서비스요금으로 충당되는 크레딧을 소유하는 계정
    2. 지출이 가장 많은 AWS 계정에 크레딧이 적용됩니다.
    3. 해당 계정 내에서 지출이 가장 많은 서비스에 크레딧이 적용됩니다.
    4. 해당 계정 내에서 지출이 가장 많은 SKU에 크레딧이 적용됩니다.

! SKU란?

설명: 제품의 고유 코드입니다. SKU는 ProductCode, UsageType 및 Operation을 결합하여 생성됩니다. 유연한 크기의 RI의 경우 SKU가 사용되었던 인스턴스를 사용합니다. 예를 들어 t2.micro 인스턴스를 사용했고, AWS가 사용량에 t2.small RI 할인을 적용한 경우 항목 SKU는 t2.micro를 포함하여 생성됩니다.

3. 단일 및 여러 계정에 AWS 크레딧 적용
> 다음 규칙은 AWS가 단일 계정 및 조직의 요금에 크레딧을 적용하는 기본 방식을 지정합니다. (크레딧 공유가 설정된 경우)

    요금 청구 주기는 매달 1일에 시작합니다.

    1일에 조직에 속하지 않은 개인이 AWS 계정을 소유하고 있다고 가정해 보겠습니다. 해당 월의 나중에 해당 개인 계정이 조직에 입사합니다. 이 경우 AWS는 해당 월의 사용량에 대해 해당 개인의 크레딧을 개별 청구서에 적용합니다. 즉, AWS는 해당 개인이 조직에 입사하는 날까지 크레딧을 적용합니다.

    조직이 월초부터 계정을 소유한 경우 AWS는 동일 월에 해당 계정이 조직을 나가더라도 지급인 계정 또는 연결된 계정이 사용한 크레딧을 조직의 청구서에 적용합니다. 월의 시작은 0:00 UTC+0 기준 1초 후에 시작됩니다. 예를 들어 특정 계정이 8월 1일에 조직을 떠나는 것으로 가정했을 때, 해당 계정이 당월 조직에 속해 있었으므로 AWS는 해당 계정이 사용한 8월 크레딧을 계속해서 조직의 청구서에 적용합니다.

    개인이 그 달 중에 조직을 떠난 경우 AWS는 다음 달 1일부터 크레딧을 개인의 계정에 적용하기 시작합니다.

    그 달의 어느 시점에서든 크레딧은 조직에 가입한 모든 계정과 공유됩니다. 그러나 조직의 공유 크레딧 풀은 해당 달의 첫날부터 조직에 속해 있었던 계정의 크레딧으로만 구성됩니다.

    예를 들어 Susan이 월 1일부터 단일 계정을 소유하다가 같은 달에 조직에 가입했다고 가정합시다. Susan이 조직에 가입한 다음 날에 크레딧을 사용한다고 할 경우, AWS는 1일부터 Susan이 조직에 가입한 날까지 발생한 사용량에 대해 계정에 크레딧을 적용합니다. 하지만 다음 달 1일부터는 AWS가 조직의 청구서에 크레딧을 적용합니다. Susan이 조직을 탈퇴할 경우 그녀가 사용하는 크레딧은 조직을 탈퇴한 다음 달 1일까지는 조직에 적용됩니다. 탈퇴 후 그 다음 달부터 AWS는 Susan의 크레딧을 조직의 청구서가 아닌 그녀의 청구서에 적용합니다.

4. AWS 크레딧 공유

> 결제 콘솔의 결제 기본 설정(Billing preferences) 페이지에서 크레딧 공유를 설정 해제할 수 있습니다. 다음 규칙은 크레딧 공유가 꺼졌을 때 크레딧이 단일 계정 및 조직의 요금에 적용되는 방식을 지정합니다.

* 요금 청구 주기는 매달 1일에 시작합니다.

* 크레딧은 크레딧을 받은 계정에만 적용됩니다.

* 청구서는 월말 기준으로 유효한 크레딧 공유 기본 설정을 사용하여 계산됩니다.

* 조직에서 지급인 계정만 크레딧 공유를 켜거나 끌 수 있습니다. 크레딧 공유 기본 설정은 조직의 모든 계정에 적용됩니다.

크레딧 공유를 끄려면
결제 콘솔을 통해 크레딧 공유를 끌 수 있습니다.