[TOC]

# 데이터베이스

## 이상 현상과 정규화

#### 이상 현상

* 정규화를 제대로 하지 못했을 때 발생하는 의도치 않은 부작용들

* 정규화를 통해 해결할 수 있다.
* 종류
  * 삭제
    * 삭제를 할 때 삭제할 대상이 아닌 데이터들도 삭제해야 하는 경우가 발생한다.
  * 수정
    * 특정 데이터의 수정이 데이터베이스의 논리적 일관성을 깨뜨릴 때
    * 특정 행의 데이터를 수정했을 때, 사실상 같은 의미를 가졌던 다른 행의 데이터들이 수정되지 않아서 일관성이 깨짐
  * 삽입
    * 행을 추가할 때 삽입을 원하지 않았던 데이터 열에 데이터를 삽입을 하지 않으면 데이터를 추가할 수 없는 상황
    * null 값을 넣어서 해결할 수 있으나, 해당 열에 대한 제약 조건에 not null이 있을 경우는 불가능

#### 정규화

- 데이터를 올바른 형태로 변경하고 분할
- 목적
  - `하나의 데이터는 한 곳에 있어야 한다.`
  - 새로운 타입의 데이터를 추가할 때 기존 데이터 구조의 변경을 최소화하기 위해.
  - 데이터베이스의 구조를 확장할 때 데이터베이스와 상호작용하는 애플리케이션에 미치는 파급 효과를 줄이기 위해
- 제1 정규화
  - 중복되는 행이 없어야 한다!
  - 테이블 분할 + 기본키 지정
  - 행들은 테이블을 값으로 가지면 안 된다.
    - (cells cannot have tables as values)
- 제2 정규화
  - 함수 종속성 제거
  - 키가 아닌 속성들은 '모든' 후보 키들에 종속 되어야 한다.
    - 후보 키들 중 일부에만 종속되면 안 된다.
- 제3 정규화
  -  key가 아닌 속성(non-prime)들은 오직 key 속성에만 의존해야 한다.
    - key가 아닌 속성에 의존하면 안 된다.

## 트랜잭션

### 개념

* DBMS에서 DB에 수행되는 독립적인 작업의 단위
* 일반적으로 데이터베이스에서 나타나는 모든 변화들을 의미.
* 작업이 실패 했을 경우, 데이터베이스를 실패 이전 상태로 복구 시키고, 데이터베이스의 **일관성을 유지**하기 위해 사용.
* 격리를 통한 데이터베이스 접근의 동시성 제어

### ACID

* atomic
  * 트랜잭션 안의 작업이 모두 완료된 상태 또는 완전히 실패한 상태
* consistent
  * DB의 제약 조건을 항상 만족해야 한다.
* isolated
  * 다른 트랜잭션에 영향을 주면 안 된다
* durable
  * 트랜잭션이 완료되면 해당 작업 사항이 영구적으로 DB에 반영되어야 한다.

### LOCK

* 데이터베이스의 일관성을 지키기 위해 데이터에 대한 동시 접근을 막기 위한 기술
* 한 레코드가 트랜잭션에서 수정 대상이 되었을 때, 락이 걸린다. 다른 트랜잭션이 같은 레코드를 수정을 시도할 수 없다.
* 락이 걸린 레코드에 대한 수정 작업이 성공적으로 수행되거나 완전히 실패했을 때, 락이 풀린다.
* 락의 크기
  * 데이터베이스
    * DB 전체 업데이트에 사용
  * 데이터베이스 파일
    * 파일 전체를 백업할 때 사용
  * 테이블
    * DDL 구문을 사용할 때 사용
  * 페이지
    * 파일을 구성하는 페이지와 블록 기룬
  * 컬럼
    * 리소스를 많이 잡아 먹어서 사용 X
  * 행
    * 가장 높은 세분화 정도.
    * 가장 자주 사용
* 종류
  * 배타 잠금(Exclusive locks)
    * 해당 레코드에 대한 락을 한 개체만 소유한다.(다른 개체들 접근 불가능)
    * 일반적으로 레코드에 대한 수정이 가해질 때 사용. 
    * 다른 잠금과 호환되지 않는다.
  * 공유 잠금(Shared locks)
    * 락이 걸려 있어도, 다른 엔티티들이 읽을 수 있음
    * SELECT를 할 떄 필요하고, SELECT가 완료되면 즉시 해제된다.
    * 다만 공유 락이 모든 개체들에 의해 완전히 해제될 때 까지, 배타 락을 걸 수 없다.
  * 갱신 잠금 (update lock)
    * 공유 잠금과는 호환되지만 다른 갱신 잠금이나 배타 잠금과는 호환되지 않는다.
    * 공유 잠금이 걸린 데이터를 수정하고 싶을 떄 사용
    * 배타 잠금이 공유 잠금과 호환되지 않는 문제점을 해결하기 위해 사용
      * 공유 잠금이 풀릴 때까지 배타 잠금은 기다려야 함(Lock wait)

### 격리 수준

* 하나의 데이터베이스 연산에 의한 변경 내용을 다른 연산에서 어느 시점에 반영시킬 것인지를 결정

#### 종류

* Serializable
  * 가장 높은 격리 수준
  * 대상 데이터에 대한 읽기/ 쓰기 연산을 수행하기 위한 lock을 요구
  * lock은 트랜잭션이 끝나면 반환된다.
  * select 질의에서 where 문을 적용할 때 range-lock을 적용한다.
    * 다른 트랜잭션은 lock이 걸린 range에 해당하는 데이터를 추가할 수 없음
    * range-lock을 보유한 트랜잭션이 동일한 select문을 날렸을 때 다른 트랜잭션에 의해 추가된 데이터까지 가져오는 상황인 phantom-read를 막기 위함
* Repeatable Reads
  * 대상 데이터에 대한 읽기  / 쓰기 락 획득
  * range-lock은 적용 X 
    * phantom reads 적용 가능성
    * 다른 트랜잭션이 해당 테이블에 데이터를 추가하면, 같은 select 조회에서 새로운 데이터가 반환될 수 있다.
  * 해당 트랜잭션에서 읽은 데이터는 트랜잭션이 끝날 때 까지 lock이 걸려 있다.
* Read commited
  * 쓰기 락만 transaction이 끝날 때 까지만 적용
  * 읽기 락은 select 명령의 시작과 끝에서만 유지.
  * 읽기 명령 대상 데이터는 해당 명령이 수행되는 시점에는 committ 되어 있음을 보장.
  * commit 되지 않은 변경들이 적용된 데이터를 읽는 것을 방지
  * non-repeatable read 발생 가능성
    * 동일 트랜잭션에서 같은 데이터를 읽는 select 문을  중간에 수정 없이 여러 번 호출했을 경우 다른 결과가 나타나는 현상

* Read uncommitted
  * 커밋되지 않은 변경들이 적용된 데이터들을 읽을 수 있다.