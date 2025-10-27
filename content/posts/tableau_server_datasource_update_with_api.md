+++
title = '태블로 Server Client 로 DataSource 변경하기'
date = 2025-10-27T16:03:51+09:00
draft = false
+++

태블로 서버를 관리할 때 [Tableau Server Client for Python](https://tableau.github.io/server-client-python/docs/api-ref#views) 를 활용하여 pragmatic 한 방법으로 편하게 관리할 수 있다.

상당 부분의 작업은 UI 를 통해 수행할 수 있지만, 시스템 전체적인 변경이나 관리는 API 를 활용하는 게 효율적이다.
예를 들어 데이터 소스로 쓰고 있는 DB 의 호스트가 변경되었을 때 해당 DB 를 사용하는 데이터 소스를 일일히 수작업으로 변경하기 보다는 API 를 활용하여 벌크로 수정할 수 있다.

```bash
# tableauserverclient 설치
pip install --upgrade pip
pip install tableauserverclient
```

```python3
# 토큰 값과 server url 로 인증

import tableauserverclient as TSC
from dataclasses import dataclass

SITE_NAME = '' # 별도의 사이트가 없으면 비워두어도 됨
TOKEN_NAME = '' # required
TOKEN_VALUE = '' # required

tableau_auth = TSC.PersonalAccessTokenAuth(TOKEN_NAME, TOKEN_VALUE, SITE_NAME)
server_url = '' # required
```

```python3
@dataclass
class Connection:
    datasource_id: str
    connection_id: str

cons = []
server = TSC.Server(server_url, use_server_version=True)
with server.auth.sign_in(tableau_auth):
    
    # 1. 워크북 커넥션을 순회하여 Clickhouse 를 데이터 소스로 쓰고 있는 커넥션을 선별한다.
    all_workbooks, pagination_item = server.workbooks.get()
    for idx, workbook in enumerate(all_workbooks):
        server.workbooks.populate_connections(workbook)
        for connection in workbook.connections:
            if connection.connection_type == 'clickhouse_jdbc':
                cons.append(Connection(datasource_id=workbook.id, connection_id=connection.id))
    
    endpoint = server.workbooks
    update_function = endpoint.update_connection
    
    # 2. 선별한 커넥션의 호스트를 변경해야하는 호스트로 수정한다.
    for con_info in cons:
        resource = endpoint.get_by_id(con_info.datasource_id)
        endpoint.populate_connections(resource)
        connections = list([x for x in resource.connections if x.id == con_info.connection_id])
        assert len(connections) == 1
        connection = connections[0]
        
        connection.server_address = '' # 변경할 clickhouse DB host
        connection.embed_password = True
        print(update_function(resource, connection).__dict__)

    print(f'{len(cons)} updated')
```

참고
- https://tableau.github.io/server-client-python/docs/api-ref#workbooks
- https://tableau.github.io/server-client-python/docs/api-ref#connections

