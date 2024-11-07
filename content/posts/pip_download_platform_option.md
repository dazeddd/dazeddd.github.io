+++
title = '[Python] 오프라인 환경에 필요한 파이썬 패키지 설치하기'
date = 2023-10-03T20:29:18+09:00
draft = false
tags = [
    "pip",
]
+++

오프라인 환경에서 파이썬으로 작업을 할 경우 패키지 파일을 다운로드 받은 후 직접 설치해야 하는 경우가 있다.

OS, CPU 등의 차이로 인한 설치 호환성 이슈가 발생할 수 있는데, 그럴 때 활용 할 수 있는 것이 pip download --platform 옵션이다.

```bash
# 설치 파일을 담을 디렉터리를 만든다
mkdir python-packages && cd python-packages

# requirements.txt 에 설치하려는 패키지들을 정리하고, macosx_10_15_x86_64 에서 설치 가능한 패키지 파일을 내려받는다
python3 -m pip freeze > requirements.txt
python3 -m pip download --only-binary=:all: --platform macosx_10_15_x86_64 -r requirements.txt
```

```bash
# 오프라인 환경에서 해당 패키지들을 설치해준다
python3 -m pip install --no-index --find-links="./" -r requirements.txt
```  

참고: https://pip.pypa.io/en/stable/cli/pip_download/
