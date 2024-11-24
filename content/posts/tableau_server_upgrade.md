+++
title = 'Tableau Server 버전 업그레이드 진행하기'
date = 2024-11-05T22:15:58+09:00
draft = false
tags = [
    "Tableau",
]
+++

사내에서 태블로 서버를 AWS EC2 에 호스팅하여 사용하고 있는데, 서버 버전이 낮은 버전이라 비교적 최신 버전으로 업그레이드를 진행하였다.

절차는 다음과 같다

1. 태블로 서버 세팅값 및 데이터 백업
```bash
tsm settings export -f <filename>.json
tsm maintenance backup -f <filename>.tsbakt
```

2. 태블로 서버 스케줄링 작업 비활성화

3. 서버 버전 선택 및 설치 (amazon linux 기준)
```bash
wget https://downloads.tableau.com/esdalt/<version>/tableau-server-<version>.x86_64.rpm 
sudo yum update -y
sudo yum install tableau-server-<version>.x86_64.rpm
```

4. 버전 업그레이드
```bash
sudo /opt/tableau/tableau_server/packages/scripts.<version_code>/upgrade-tsm --accepteula
```

**Reference**
- https://help.tableau.com/current/server-linux/en-us/backup_restore.htm#backing-up-tableau-server-for-recovery
- https://help.tableau.com/current/server-linux/en-us/server-upgrade-baseline-disable-tasks.htm
- https://www.tableau.com/support/releases/server
- https://help.tableau.com/current/server-linux/en-us/server-upgrade-baseline-singlenode-setup.htm
