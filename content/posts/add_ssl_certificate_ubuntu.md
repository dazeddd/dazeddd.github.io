+++
title = 'Ubuntu 에서 SSL 인증서 등록하는 법'
date = 2023-02-07T10:35:17+09:00
draft = false
tags = [
    "ssl",
]
+++

wsl ubuntu 에서 작업을 하는 데 SSL 인증 오류가 발생해서, 해당 도메인의 SSL 인증서를 등록해주는 방법으로 해결하였다.

<!-- ![pypi](/pypi_ssl_certificate.png) -->
<img src="/pypi_ssl_certificate.png" width="400">

pypi.org domain 의 ssl 인증서를 등록한다고 했을 때 위와 같은 방법으로 SSL 인증서를 내려받는다

```bash
mv example.crt /usr/local/share/ca-certificates
sudo update-ca-certificates
```