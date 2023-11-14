+++
title = '[Python] certifi 패키지를 활용한 requests SSL 에러 해결'
date = 2023-11-14T11:29:24+09:00
draft = false
+++

python requests 패키지 **2.16 버전 이후 부터는** certifi 모듈을 사용하여 CA 인증서를 관리한다.

requests.get(url) 과 같이 호출을 할 때 SSL 인증 오류가 발생한다면 

```bash
echo $(python3 -m certifi)
```
로 나오는 경로에 해당 인증서 내용을 등록해주어야 한다.

참고: https://requests.readthedocs.io/en/latest/user/advanced/#ca-certificates 