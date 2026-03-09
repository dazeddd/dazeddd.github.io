+++
title = 'AWS Athena 로 S3 glacier 에서 복원한 파일 조회 시 유의점'
date = 2026-03-09T22:49:27+09:00
draft = false
tags = [
    "AWS Athena", "S3 Glacier"
]
+++

S3 Glacier Deep Archive 에 저장된 parquet 포맷의 파일들을 읽기 위해서는 일단 복원 작업을 해야했다.

특정 파일만 복원하는 게 아니라 특정 경로의 모든 parquet 파일을 복원하기 위해서 S3 Batch Operations 을 활용하여 일괄 복원하였고,
standard retrieval 기준으로 대략 12시간이 소요되었다.

복원 후 parquet 파일의 데이터를 연동하기 위해, EXTERNAL TABLE 및 CTAS 등으로 테이블을 생성하여 데이터를 조회하려고 하는데
이상하게도 결과값이 나오지 않았다.

에러 로그나 정보성 로그 없이 결과값만 안나오는 상황이어서 여러모로 헤메다가, 아래와 같은 테이블 프로퍼티를 설정해 주어야 함을 발견하였다.<br>
아테나가 자동으로 Glacier 에서 복원된 파일을 인식하는 것은 아니어서, 별도로 테이블 설정을 해주어야 하는 것으로 보인다.

```sql
ALTER TABLE table_name SET TBLPROPERTIES ('read_restored_glacier_objects' = 'true')
```

혹시 Glacier 에서 복원된 파일을 읽을 때 결과값이 나오지 않는다면, 위 사황을 누락하지 않았는지 체크하면 좋을 것 같다.

**참고 링크**
- https://docs.aws.amazon.com/athena/latest/ug/querying-glacier.html
- https://docs.aws.amazon.com/athena/latest/ug/parquet-serde.html
- https://docs.aws.amazon.com/athena/latest/ug/ctas-insert-into-etl.html