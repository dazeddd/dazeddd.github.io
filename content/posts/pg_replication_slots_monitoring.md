+++
title = 'PostgreSQL logical replication 세팅 시 WAL Disk 관리'
date = 2025-10-27T13:47:21+09:00
draft = false
+++

Primary DB 를 리플리케이션 하는 Standby DB 나 debezium 을 활용한 CDC 등을 위해
PostgreSQL logical replication 을 세팅하여 운영하는 중에 WAL 디스크가 무한정 커지는 문제를 발견하였다.

원본 DB 와의 네트워크가 불안정하거나 연결이 끊겼을 때 리플리케이션 DB 가 WAL 파일을 읽어오지 못하면, 원본 DB 에서 이를 감지하고 복구를 위해 WAL 을 무한정 저장해놓는다.

PostgreSQL 13 버전부터는 max_slot_wal_keep_size 설정 값을 활용하여 WAL 저장 용량이 무한정 커지는 것을 방지할 수는 있지만, max_slot_wal_keep_size 를 초과할 경우
WAL 에 기록된 변경사항이 유실되고 결과적으로 리플리케이션 데이터의 정합성이 깨지는 문제가 발생할 수 있다.

실무에서는 OVH 클라우드에 세팅되어 있는 PostgreSQL 서버를 AWS 의 RDS 가 안정적으로 리플리케이션 하여 복제 서버로서 작동할 수 있도록 모니터링이 필요하였다.

pub-sub 으로 동작하는 logical replication 의 현재 상태를 확인하기 위해서는 기본적으로 원본 DB 의 pg_replication_slots 테이블을 확인하면 된다.
해당 테이블의 active 칼럼이 false 인 slot 이 있다면 해당 pub-sub 은 모종의 이유로 작동하지 않고 있는 것이고, 정상으로 복구가 될 때까지 WAL 디스크는 무한정 커지고 있는 상태이다.

이를 감지하기 위해 에어플로우상에서 해당 테이블의 active 칼럼을 체크하는 로직을 30분마다 구동되도록 스케줄링하여, 리플리케이션의 지연을 확인하고 WAL 디스크가 무한정 커지는 문제를 방지할 수 있다

```sql
select slot_name from pg_replication_slots where not active
```

