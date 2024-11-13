+++
title = 'PostgreSQL default previlege 설정하기'
date = 2024-11-05T21:40:13+09:00
draft = false
+++

Postgresql 을 운영하다 보면 테이블이나 시퀀스 등에 기본으로 부여되는 권한을 설정하고 싶을 때가 있다

예를 들어 master 유저로 접속하여 public 스키마에 테이블을 생성할 때, api_user 에 기본으로 SELECT, INSERT, UPDATE (arw) 권한을 부여하고 싶다면 아래와 같이 실행한다
```postgresql
ALTER DEFAULT PRIVILEGES FOR master IN SCHEMA public 
    GRANT SELECT, INSERT, UPDATE ON TABLES TO api_user;
```

위처럼 기본 부여 권한을 설정해놓으면 테이블이나 시퀀스 등을 생성할 때마다 반복되는 권한 부여 작업을 생략할 수 있어 효율적이고 안정적으로 권한 관리를 할 수 있다.

**Reference**
- https://www.postgresql.org/docs/current/sql-alterdefaultprivileges.html
- https://www.postgresql.org/docs/current/ddl-priv.html



