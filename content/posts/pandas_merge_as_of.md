+++
title = '[pandas] 가장 가까운 키값으로 조인하고 싶을 때 유용한 pandas.merge_asof'
date = 2022-04-23T12:17:49+09:00
draft = false
tags = [
    "pandas",
]
+++

거래소 데이터를 처리하면서 거래 체결 데이터와 환율 값을 시간으로 매칭해야 하는 케이스가 생겼다.
하지만 문제는 각각의 거래 체결 시간과 주기적으로 업데이트 하는 환율 데이터의 시간이 **오차 없이 매칭되기가 힘들다는 것이다.**
여러 모로 방법을 모색하다가 pandas 에서 제공하는 **merge_asof** 라는 함수를 활용하여 수월하게 작업을 수행할 수 있었다.

```python
pandas.merge_asof(left, right,
    on=None, left_on=None, right_on=None,
    left_index=False, right_index=False,
    by=None, left_by=None, right_by=None,
    suffixes=('_x','_y'),
    tolerance=None,
    allow_exact_matches=True,
    direction='backward')
```

참고: https://pandas.pydata.org/docs/reference/api/pandas.merge_asof.html#pandas.merge_asof

left 와 right 에는 거래 데이터 프레임과 환율 데이터 프레임이 각각 들어가고, left_on 과 right_on 에는 거래 체결 시간 칼럼명과 환율 기록 시간 칼럼명이 들어간다.
거래 체결 시간 기준 가장 최근의 과거 환율 값을 매칭 시켜 줘야하니 direction = 'backward' 를 그대로 사용해준다.

**주의할 점은** 위 함수로 merge 하기 전에 조인에 쓰이는 키가 데이터 프레임 내에서 **정렬 상태**여야 한다는 것이다. df.sort_values() 등으로 데이터 프레임을 조인 키로 정렬 후 merge_asof 를 활용해야 기대하는 결과를 얻을 수 있다.

그리고 참고로 판다스가 시간 값을 datetime 으로 인식하지 못한다면 pd.to_datetime 으로 형변환을 한 후 조인을 하면 된다.




