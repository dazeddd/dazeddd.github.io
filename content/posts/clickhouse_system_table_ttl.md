+++
title = 'ClickHouse 시스템 로그 테이블 TTL 설정'
date = 2025-10-23T16:41:19+09:00
draft = false
+++

서비스나 데이터마트용으로 클릭하우스를 설치하여 운영하다 보면 의도치 않게 system db 의 로그 테이블이 과도하게 커지는 문제가 있다.

실제 운영 데이터의 용량은 크지 않아도 시스템 로그 테이블의 용량이 방대해지면 정작 필요한 데이터가 아닌 로그 데이터로 디스크가 소진되는 문제가 있기에 조치가 필요하였다.

클릭하우스 system db 에 query_log, metric_log, part_log, trace_log 등 다양한 용도의 로그 저장 테이블들이 있는데, 
[관련 문서](https://clickhouse.com/docs/operations/system-tables) 에서 log 테이블의 용도를 확인하고 로그 성격에 맞게 TTL 설정을 한다.

TTL 설정은 관리의 용이성을 위해 /etc/clickhouse-server/config.d/ 에 ttl.xml 파일을 생성하여 아래와 같이 설정한다.

```xml
<clickhouse>
    <query_log>
        <table>query_log</table>
        <partition_by>toYYYYMM(event_date)</partition_by>
        <ttl>event_date + INTERVAL 7 DAY DELETE</ttl>
    </query_log>
</clickhouse>
```

아예 해당 로그 테이블이 쌓이지 않게 하고 싶으면 아래와 같은 disable.xml 파일을 생성하여 적용하면 된다.
```xml
<clickhouse>
    <query_log remove="1"/>
</clickhouse>
```

참고
- https://clickhouse.com/docs/operations/system-tables/overview
- https://clickhouse.com/docs/operations/system-tables



