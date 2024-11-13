+++
title = 'PostgreSQL 서버 최적화를 위해 자주 사용하는 명령어'
date = 2024-11-10T16:29:15+09:00
draft = false
+++

1. __ANALYZE__<br>
ANALYZE 명령어는 테이블이나 칼럼에 대한 통계 정보(pg_catalog.pg_statistic)를 업데이트 해주어 쿼리 플래너가 최적의 쿼리 실행 계획을 세울 수 있도록 도움을 준다.<br>
VERBOSE 옵션과 함께 쓰면 진행 로그를 실시간으로 볼 수 있고, 진행 정도를 확인하고 싶으면 pg_stat_progress_analyze 에서 조회하면 된다

```postgresql
ANALYZE [ VERBOSE ] [ table_and_columns [, ...] ]
```

2. __VACUUM__<br>
VACUUM 은 테이블에 남아있는 dead tuple 을 청소하여 저장 공간을 확보하는 역할을 한다. (dead tuple 을 발생시키는 MVCC 매커니즘에 대해서는 다른 포스트에서 다루어보도록 하겠다) <br>
다양한 옵션과 함께 사용할 수 있는데, 옵션 없이 플레인한 VACUUM 을 하면 테이블 락 없이 재사용 공간을 확보할 수 있고, 
FULL VACUUM 은 테이블 락과 함께 OS 상에 저장 공간을 돌려주는 역할을 한다.<br>
위에서 살펴본 ANALYZE 명령과 함께 사용할 수도 있고, pg_stat_progress_vacuum 를 통해 진행 상황을 확인할 수 있다. 또한 테이블 별 dead tuple 과 live tuple 의 
현재 수치 및 비율 등을 확인하고 싶다면 pg_stat_user_table 을 조회하여 확인할 수 있다.

```postgresql
VACUUM [ ( option [, ...] ) ] [ table_and_columns [, ...] ]
```


3. __REINDEX__<br>
해당 명령어는 특정 index 가 corrupted 나 bloated 상태가 되어 제 역할을 하지 못하는 것이 의심될 때 사용할 수 있다. <br>
REINDEX 는 기본적으로 해당 테이블에 대한 쓰기 잠금이 걸리기 때문에 잠금 없이 긴급하게 REINDEX 를 해야하는 경우는 CONCURRENTLY 옵션과 함께 실행할 수 있다.

```postgresql
REINDEX [ ( option [, ...] ) ] { INDEX | TABLE | SCHEMA } [ CONCURRENTLY ] name
```

참고:
- https://www.postgresql.org/docs/current/sql-analyze.html
- https://www.postgresql.org/docs/current/catalog-pg-statistic.html
- https://www.postgresql.org/docs/current/sql-vacuum.html
- https://nrise.github.io/posts/postgresql-autovacuum/
- https://www.postgresql.org/docs/current/sql-reindex.html