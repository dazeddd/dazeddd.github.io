+++
title = 'boto3 활용하여 AWS DynamoDB 데이터 백업 및 삭제'
date = 2025-10-27T17:17:35+09:00
draft = false
+++

boto3 는 python 으로 aws 리소스를 용이하게 관리할 수 있도록 해주는 라이브러리이다.

아래 예제는 boto3 로 dynamodb 테이블 내 불필요 데이터를 백업한 후 해당 데이터를 삭제하는 작업이다.

```python3
# boto3 client 인증

import boto3
import boto3.session
from boto3.dynamodb.conditions import Key, Attr
from tqdm import tqdm
import pandas as pd
from datetime import datetime, timezone

session = boto3.session.Session(
    aws_access_key_id=aws_access_key_id, # required
    aws_secret_access_key=aws_secret_access_key, # required
    region_name=region_name # required
)

dynamodb = session.resource('dynamodb')
table = dynamodb.Table(table_name) # required
```


```python3
# 해당 파티션의 특정 시간 이하 데이터 백업 및 삭제

partition_keys = [
    f'{market}#1h',
    f'{market}#4h',
    f'{market}#6h',
    f'{market}#12h',
    f'{market}#1D',
    f'{market}#1W',
    f'{market}#1M'
]

for key in partition_keys:

    print('\n')
    print(key)
    
    all_items = []
    exclusive_start_key = None

    while True:
        if exclusive_start_key:
            response = table.query(
                KeyConditionExpression=Key('market_id#interval').eq(key) & Key('time').lt(1711584000),
                ExclusiveStartKey=exclusive_start_key
            )
        else:
            response = table.query(KeyConditionExpression=Key('market_id#interval').eq(key) & Key('time').lt(1711584000))
        
        all_items.extend(response['Items'])
        
        print(response['Count'])

        if 'LastEvaluatedKey' in response:
            exclusive_start_key = response['LastEvaluatedKey']
        else:
            break

    print(f"Total items fetched: {len(all_items)}")


    # csv 로 데이터 백업
    if all_items:
        df = pd.DataFrame(data=all_items)
        df.to_csv(f'./csv/{key}_{env}.csv')
        
        sorted_items = sorted(all_items, key=lambda x: float(x['time']))

        # 시간 검증
        lastest_time = datetime.fromtimestamp(float(sorted_items[-1]['time']), tz=timezone.utc).replace(tzinfo=None)
        maximum_time = datetime.strptime('2024-03-28', '%Y-%m-%d')
        print(sorted_items[-1])
        print('latest time:', lastest_time)
        assert lastest_time <= maximum_time

    # 데이터 정리
    with table.batch_writer() as batch:
        for item in tqdm(all_items):
            batch.delete_item(Key={
                'market_id#interval': item['market_id#interval'],
                'time': item['time']  # required if sort key exists
            })
        print(f"Deleted: {len(all_items)}")
```