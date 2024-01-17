# [ 2. 설치와 설정 ]
- MySQL 서버 시작
  - linux> systemctl start mysqld
- MySQL 서버 종료
  - linux> systemctl stop mysqld

- MySQL 서버 종료 시 모든 커밋된 내용 데이터 파일에 기록
  - mysql> SET GLOBAL innodb_fast_shutdown=0; 
  - linux> systemctl stop mysqld.service

- MySQL 서버 업그레이드 시 주의 사항
  - 데이터 파일을 그대로 두고 업그레이드 하는 '인플레이스드 업그레이드'는 마이너 버전에서만 가능하다.
  - 두 단계 이상 업그레이드 시에는 mysqldump 프로그램으로 데이터 백업 후 새로우 구축된 MySQL서버에 데이터를 적재하는 '논리적 업그레이드'를 진행하는 것이 올바르다.

 - MySQL 시스템 변수 (적용 범위에 따른 구분)
   - 글로벌 변수 : 전체적으로 영향을 미치는 시스템 변수 (주로 서버 자체에 관련된 설정)
   - 세션 변수 : MySQL 클라이언트가 접속할 때 기본으로 부여하는 옵션의 기본값을 제어하는 시스템 변수

 - MySQL 시스템 변수 (기동 중 변경 가능 여부에 따른 구분)
   - 동적 변수 : SET 명령으로 기동중에 변경 가능 (주의 : 영구적으로 적용 시 my.cnf 파일도 반드시 변경)
   - 정적 변수 : 기동 중에 변경 불가능한
- MySQL 8.0 부터 등장한 SET PERSIST :
  - 변경된 값을 즉시 적용 + 별도의 설정 파일(mysqld-auto.cnf)에 변경 내용을 기록 (서버 재시작시 사용)
