+++
title = 'pg_dump, pg_restore 활용 팁'
date = 2024-11-09T16:46:03+09:00
draft = true
tags = [
    "postgresql"
]
+++

평소 Postgresql 을 메인 DB로 사용하다 보니, 데이터 마이그레이션 작업이나 스키마 정리 작업을 할 때 pg_dump, pg_restore 를 자주 사용한다.

해당 명령어를 사용하면서 알아두면 유용한 몇가지 옵션들을 공유하고 싶어서 기록합니다.

**pg_dump**: 기본적인 덤프는 아래와 같이 실행한다.
```bash
pg_dump -h {db_host} -d {db_name} -p {port} -U {user_name} -F c > db_dump.sql
```
**그 외 옵션**<br>
-F (--format=): c (custom) 옵션을 주면 pg_restore 가 데이터를 복원할 때 사용할 수 있는 압축된 포맷으로 파일을 생성한다.<br>
-s (--schema-only): 데이터 없이 스키마만 덤프하고 싶을 때 활용할 수 있고, -F p 옵션과 함께 사용하면 사람이 알아볼 수 있는 평문으로 파일을 생성해주어 현재 스키마를 정리하고 싶을 때 유용하다.<br>
-t (--table), -n (--schema): 특정 테이블 혹은 스키마만 별도로 덤프를 받을 수 있다.


**pg_restore**: 기본적인 복원은 아래와 같이 실행한다.
```bash
pg_restore -h {db_host} -d {db_name} -p {port} -U {user_name} -f db_dump.sql
```
**그 외 옵션**<br>
-C (--create): 해당 옵션 없이 복원을 하게 되면 특정 데이터베이스에 대한 접근 권한이나 설정값들이 복원되지 않으니 추가해주는 것이 좋다.<br>
-v (--verbose): verbose 옵션을 주면 복원이 진행되는 상황을 로그를 통해 볼 수 있다.

대부분의 옵션들이 두 명령어 간에 공통으로 사용할 수 있는 옵션이니 아래 링크를 참고하여 필요에 따라 골라 사용하면 좋을 것 같다.

**참고**
- https://www.postgresql.org/docs/current/app-pgdump.html
- https://www.postgresql.org/docs/current/app-pgrestore.html


