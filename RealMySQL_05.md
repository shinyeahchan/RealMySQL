# [ 5. 트랜잭션과 잠금 ]
## 기초 개념
- **트랜잭션** : 논리적인 작업 셋을 모두 완벽하게 처리 못할 경우에는 원 상태로 복구하는 개념이다. (Partial update 방지), 작업의 완전성 보장
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

- JAVA에서의 DB 트랜잭션 코드 예시
![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/b2db0b33-bc8d-4a99-b107-d05354ef21fd)

- JAVA + Spring 에서의 DB 트랜잭션 코드 예시
  
    **@Transactinal** 어노테이션 사용
![image](https://github.com/shinyeahchan/RealMySQL/assets/93124649/aea6dcbb-eada-44e5-a1b7-1a53ac7599fb)
### **ACID**
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
