# [ 5. 트랜잭션과 잠금 ]
## 트랜잭션
- **트랜잭션** : 논리적인 작업 셋을 모두 완벽하게 처리 못할 경우에는 원 상태로 복구하는 개념이다. (Partial update 방지), 작업의 완전성 보장
- MySQL에서 InnoDB엔진은 트랜잭션을 지원하기 때문에, 쿼리 중 일부라도 오류 발생시 그 전으로 복구한다.
- 꼭 필요한 최소의 코드에만 적용하여, 트랜잭션의 범위를 최소화하는 것을 권장한다.
```mysql
mysql
   START TRANSACTION;  // 트랜젝션 시작
   COMMIT;             // 지금까지 작업한 내용을 DB에 영구적으로 저장 + 트랜잭션 종료
```
```mysql
mysql
   START TRANSACTION;  // 트랜젝션 시작
   ROLLBACK;           // 지금까지 작업들 모두 취소, 트랜잭션 이전 상태로 되돌린다. + 트랜잭션 종료
```
- MySQL에서는 autocommit 이 기본적으로 enabled 되어 있어서 자동으로 커밋(오류 시 롤백)을 진행한다.
- ```START TRANSACTION;```을 직접 사용시에는 autocommit이 잠시 off된다.

#### 참고1) JAVA에서의 DB 트랜잭션 코드 예시
>![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/b2db0b33-bc8d-4a99-b107-d05354ef21fd)

#### 참고2) JAVA + Spring 에서의 DB 트랜잭션 코드 예시
>**@Transactinal** 어노테이션 사용
![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/aea6dcbb-eada-44e5-a1b7-1a53ac7599fb)
#### 참고3) **ACID** 원칙
 - **A**tomicity(원자성)
   : 트랜잭션의 작업이 **부분적으로 실행되거나 중단되지 않는 것**을 보장하는 것을 말한다. ( **ALL or NOTHING** )
   > **트랜잭션의 단위(언제 commit, 언제 rollback)는 개발자가 챙겨야한다.**
 - **C**onsistency(일관성)
   : 트랜잭션은 데이터베이스의 일관성을 유지해야 한다. 즉, DB에 정의된 **rule을 트랜잭션이 위반했다면 ROLLBACK** 해야 한다.
   > DBMS는 DB에 정의된 rule을 위반했는지 commit 전에 확인하고 알려준다.
   
   > **그 외 application 관점에서 트랜잭션이 consistent하게 동작하는지는 개발자의 역할이다.**
 - **I**solation(고립성)
   : **동시에 실행되는 여러 트랜잭션이 서로에게 영향을 미치지 않도록 격리**되어야 한다. 
   > DBMS는 여러 종류의 isolation level (격리 수준)을 제공한다.
   
   > **개발자는 어떤 level로 동작시킬지 결정한다.**
 - **D**urablility(지속성)
   : **commit된 트랜잭션은 DB에 영구적으로 저장**한다. 즉, 비휘발성 메모리(HDD, SSD)에 저장한다.
   > DBMS가 담당한다.
---
## 잠금(Lock)
- **잠금** : 여러 커넥션에서 동시에 동일한 자원(레코드or테이블)을 요청할 경우, 순서대로 한 시점에는 하나의 커넥션만 변경할 수 있게 한다. (동시성 제어)
- MySQL 엔진의 잠금 : 글로벌 락, 테이블 락, 네임드 락, 메타데이터 락
- InnoDB 스토리지 엔진의 잠금 : **레코드 락** , 갭 락, 넥스트 키 락, 자동 증가 락(Auto Increment lock)
- InnoDB의 잠금(레코드 락)은 레코드를 잠그는 것이 아니라 인덱스를 잠그는 방식으로 처리된다. (변경할 레코드를 찾기 위해 검색한 인덱스의 레코드를 모두 락을 건다)
---
## 격리 수준
- READ UNCOMMITTED
  - 일반적으로 사용되지 않는 격리 수준.
  - 트랜잭션의 변경 내용이 COMMIT, ROLLBACK 여부에 상관없이 다른 트랜잭션에서 보인다. 즉, DIRTY READ가 발생한다.
- READ COMMITTED
   - 오라클DBMS 등에서 기본으로 가장 많이 사용되는 격리 수준이다.
   - COMMIT이 완료된 데이터만 다른 트랜잭션에서 조회 가능하다.
   - 커밋 전엔, 다른 트랙잭션에서는 언두 로드로 백업된 기존 값을 조회한다.
   - NON-REPEATABLE READ 이라는 문제 발생가 발생한다. (아래 그림 참고)
     >![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/018d277b-64ad-487d-a864-c7909f0f8568)
- REPEATABLE READ
   - MySQL의 InnoDB 스토리지 엔진에서 기본으로 사용되는 격리 수준이다.
   - 자신보다 작은 트랜잭션 번호의 변경사항만 보게 함으로써 NON-REPEATABLE READ 현상이 발생하지 않는다.
     >![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/31be945b-5efc-4261-8782-2051093fb9a6)
   - 갭 락과 넥스트 키락 덕분에 PHANTOM READ 현상도 발생하지 않는다.
     > 예외적으로, SELECT ... FOR UPDATE나 SELECT ... FOR SHARE 등 잠금을 동반한 SELECT 쿼리에서는 PHANTOM READ 현상이 발생할 수 있다. 언두 레코드에는 잠금을 걸 수 없기 때문이다.
- SERIALIZABLE
   - 가장 엄격한 격리수준 -> 동시 처리 성능이 매우 떨어진다.
   - 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서 절대 접근할 수 없는 것이다.
     >  읽기 작업에도 공유 잠금(읽기 잠금)을 획득해야 하기 때문이다. 
