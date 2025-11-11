+++
title = 'postgres docker image 활용하여 pg_dump 버전 조절하기'
date = 2025-11-11T15:48:18+09:00
draft = false
+++

pg_dump 를 하는 경우에 pg_dump 버전 문제로 인해 해당 명령어가 작동하지 않는 경우가 있다.

예를 들어 작업 환경에서 15 버전의 pg_dump 를 쓰는데 16 버전의 서버를 dump 하려고 할때 아래와 같은 메시지가 뜬다

```bash
pg_dump: error: aborting because of server version mismatch
```

작업 환경의 psql 버전을 업그레이드 하기에는 번거로우니 postgres 도커 이미지를 활용하여 간단하게 16 버전의 pg_dump 를 사용할 수 있다.

```bash
# 이미지 내려받은 후 컨테이너 생성
docker pull postgres@16
docker run --name postgres-for-psql -e POSTGRES_PASSWORD=$pgpassword -d postgres:16
```

```bash
# 16 버전의 pg_dump 를 활용할 수 있다.
docker exec -e PGPASSWORD=$pgpassword {container-id} pg_dump -h {host} -U {user} -d {db} -F custom -v > {dump_file_name}.sql
```
