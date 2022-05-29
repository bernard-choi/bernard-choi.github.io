---
layout: post
title: "[Docker] Docker Container를 root가 아닌 일반 유저로 실행하는 법"
subtitle: "[Docker] Docker Container를 root가 아닌 일반 유저로 실행하는 법"
categories: Development
tags: Docker
comments: true


---

## sudo 없이 사용하기
docker는 기본적으로 root권한이 필요. root가 아닌 사용자가 sudo 없이 사용하려면 해당 사용자를 docker 그룹에 추가.

```shell
sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한 주기
sudo usermod -aG docker your-user # your-user 사용자에게 권한주기
```