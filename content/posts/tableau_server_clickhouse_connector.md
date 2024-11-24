+++
title = '태블로 서버에 Clickhouse JDBC 커넥터 설치하기'
date = 2024-11-07T23:08:29+09:00
draft = false
tags = [
    "Tableau",
]
+++

태블로 서버상에서 클릭하우스 DB 에 연결하여 시각화를 진행하여야 했다.

내가 사용하고 있는 태블로 서버 버전에서는 클릭하우스 연결을 기본으로 제공하지 않기 때문에 아래 링크에서 JDBC 파일을 내려 받아 설치하였다. 

1. https://github.com/ClickHouse/clickhouse-java/releases/tag/v0.4.6

```bash
sudo mkdir -p /opt/tableau/tableau_driver/jdbc

wget https://github.com/ClickHouse/clickhouse-java/releases/download/v0.4.6/clickhouse-jdbc-0.4.6-shaded.jar

sudo cp clickhouse-jdbc-0.4.6-shaded.jar /opt/tableau/tableau_driver/jdbc

# can be readable by tableau user
sudo chmod 755 /opt/tableau/tableau_driver/jdbc/clickhouse-jdbc-<version>-shaded.jar.jar
```

2. https://github.com/ClickHouse/clickhouse-tableau-connector-jdbc/releases

```bash
wget https://github.com/ClickHouse/clickhouse-tableau-connector-jdbc/releases/download/v0.3.1/clickhouse_jdbc_0.3.1.taco ./

sudo cp clickhouse_jdbc_0.3.1.taco /opt/tableau/connectors
```

3. 태블로 서버를 재시작해주면 데이터소스를 생성할 때 해당 커넥터를 사용할 수 있음을 확인할 수 있다.

```bash
tsm restart
```

참고: https://github.com/ClickHouse/clickhouse-tableau-connector-jdbc?tab=readme-ov-file

