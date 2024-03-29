# MVCC(다중 버전 동시성 제어)

## 동시성 제어

    동시성 제어란 DBMS가 다수의 사용자 사이에서 동시에 작용하는 다중 트랜잭션의 상호간섭 작용에서 Database를 보호하는 것을 의미한다. 일반적으로 동시성을 허용하면 일관성이 낮아지게 되며 이를 그래프로 나타내면 아래와 같다.
<img src="./Images/ConcurrencyControl.png">
    
    다수 사용자의 동시 접속을 위해 DBMS는 동시성 제어를 할 수 있도록 Lock 기능과 SET TRANSACTION 명령어를 이용해 트랜잭션의 격리성 수준을 조정할 수 있는 기능도 제공한다. 이렇게 동시성을 제어하는 방법에는 비관적 동시성 제어와 낙관적 동시성 제어가 있다.

> 비관적 동시성 제어(Pessimistic Concurrency Control)
* 사용자들이 같은 데이터를 동시에 수정할 것이라고 가정
* 데이터를 읽는 시점에 Lock을 걸고, 트랜잭션이 완료될 때까지 이를 유지
* SELECT 시점에 Lock을 거는 비관적 동시성 제어는 시스템의 동시성을 심각하게 떨어뜨릴 수 있어서 wait 또는 nowait 옵션과 함께 사용해야 함

> 낙관적 동시성 제어(Optimistic Concurrency Control)
  * 사용자들이 같은 데이터를 동시에 수정하지 않을 것이라고 가정
  * 데이터를 읽는 시점에 Lock을 걸지 않는 대신 수정 시점에 값이 변경됐는지를 반드시 검사
    
        동시성 제어의 목표는 동시에 실행되는 트랜잭션 수를 최대화 하면서 입력, 수정, 삭제, 검색 시 데이터의 무결성을 유지하는데 있다. 하지만 읽기 작업에 공유 Lock을 사용하는 일반적인 Locking 메커니즘은 구현이 간단한 반면에 아래와 같은 문제점을 가지고 있다.

> Locking 메커니즘의 문제점
* 읽기 작업과 쓰기 작업이 서로 방해를 일으키기 때문에 동시성 문제가 발생
* 데이터 일관성에 문제가 생기는 경우도 있어서 Lock을 더 오래 유지하거나 테이블 레벨의 Lock을 사용해야 하고, 동시성 저하가 발생한다

이러한 문제점들을 해결하기 위해 MVCC(Multi-Version Concurrency Control, 다중 버전 동시성 제어)가 탄생하게 되었다.

## MVCC(Multi-Version Concurrency Control, 다중 버전 동시성 제어)
> MVCC란?

    MVCC는 동시 접근을 허용하는 데이터베이스에서 동시성을 제어하기 위해 사용하는 방법 중 하나이다. MVCC 모델에서 데이터에 접근하는 사용자는 접근한 시점에서 데이터베이스의 Snapshot을 읽는다. 이 snapshot 데이터에 대한 변경이 완료될 때 (트랜잭션이 commit 될 때) 까지 만들어진 변경사항은 다른 데이터베이스 사용자가 볼 수 없다. 이제 사용자가 데이터를 업데이트하면 이전의 데이터를 덮어 씌우는게 아니라 새로운 버전의 데이터를 UNDO 영역에 생성한다. 대신 이전 버전의 데이터와 비교해서 변경된 내용을 기록한다. 이렇게 해서 하나의 데이터에 대해 여러 버전의 데이터가 존재하게 되고, 사용자는 마지막 버전의 데이터를 읽게 된다. 이러한 구조를 지닌 MVCC의 특징을 정리하면 아래와 같다.

    * 일반적인 RDBMS보다 매우 빠르게 작동
    * 사용하지 않는 데이터가 계속 쌓이게 되므로 데이터를 정리하는 시스템이 필요
    * 데이터 버전이 충돌하면 애플리케이션 영역에서 이러한 문제를 해결해야 함

    MVCC의 접근 방식은 잠금을 필요로 하지 않기 때문에 일반적인 RDBMS보다 매우 빠르게 작동한다. 또한 데이터를 읽기 시작할 때, 다른 사람이 그 데이터를 삭제하거나 수정하더라도 영향을 받지 않고 데이터를 사용할 수 있다. 대신 사용하지 않는 데이터가 계속 쌓이게 되므로 데이터를 정리하는 시스템이 필요하다. MVCC 모델은 하나의 데이터에 대한 여러 버전의 데이터를 허용하기 때문에 데이터 버전이 충돌될 수 있으므로 애플리케이션 영역에서 이러한 문제를 해결해야 한다. 또한 UNDO 블록 I/O, CR Copy 생성, CR 블록 캐싱 같은 부가적인 작업의 오버헤드 발생한다. 이러한 구조의 MVCC는 문장수준과 트랜잭션 수준의 읽기 일관성이 존재한다.

출처: https://mangkyu.tistory.com/53 