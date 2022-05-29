---
layout: post
title: "[Docker] Docker Network" 
subtitle: "[Docker] Dockerfile Network"
categories: Development
tags: Docker
comments: true


---

## 1. 호스트-컨테이너 네트워크

- 아래는 호스트와 도커 엔진 간 네트워크 구성도

![docker_network1](https://yunsikus.github.io/assets/img/post_img/docker_network1.jpg) 

### **1. NIC(Network Interface Controller)**
- 흔히 우리가 LAN 카드라고 부르는 것이 바로 NIC. 호스트와 네트워크간 데이터를 송수신하는 인터페이스 역할 수행
- 물리적으로 구성된 NIC는 ens33으로 인식됨. 구버전 리눅스 배포판에서는 eth0로 인식외었고 시중의 많은 도서도 eth0로 기술되어 있는데 모두 물리적인 NIC로 보면 됨

### **2. NAPT(Network Address Port Translation)**
- NAPT는 IP와 Port를 변환하는 기술. 도커 엔진이 컨테이너에 부여한 IP는 사설 IP로서 호스트에서 직접 접근이 불가능하기 때문에 변환이 필요. Host의 각 포트에 컨테이너의 IP와 포트를 맵핑 시켜주는 것이 NAPT의 역할.

### **3. docker0**
- bridge라는 형태의 네트워크이며 도커 엔진을 실행하면 자동으로 생성됨. 컨테이너를 실행하면 내부의 모든 포트를 `docker0` 라는 가상 bridge에 개방. 호스트는 NIC에서 직접 컨테이너와 연결하는 것이 아니라 이 bridge를 경유하게 됨. 포트를 통해 각 컨테이너에 접근할 수 있도록 다리를 놔주는 역할

### **4. vethOOO**
- 컨테이너를 띄우고 Host에서 `ifconfig`명령어를 수행하면 `docker0`, `lo`, `ens33` 이외에 `veth`로 시작하는 것이 새롭게 생긴 것을 확인할 수 있다. 이는 컨테이너가 Host와 통신하기 위한 가상의 NIC
- `veth + 난수` 의 형태로 생성
- 구조고 내 컨테이너에 `eth0` 이라고 표시된 이유는 Host 입장이 아닌 컨테이너 입장에서 가상 NIC를 `eth0` 라고 인식하기 때문

---

## 2. 컨테이너-컨테이너 네트워크

서비스를 구축하는 경우, 여러개의 컨테이너들이 통신이 가능하도록 설정해주어야 한다. 

### 1. `bridge`

![docker_network2](https://yunsikus.github.io/assets/img/post_img/docker_network2.jpg) 

- 도커 엔진 내부에서 동작하는 `bridge` 네트워크는 위와 같이 구성되어 있다. 
  
- `docker0`를 Gateway로 하여 컨테이너가 생성된 순서대로 IP가 부여됨. 

- 기본적으로 컨테이너를 구동할 때 별도의 네트워크 설정을 하지 않으면 모두 이 `docker0`라는 `bridge` 네트워크에 연결이 됩니다. 
  
- 같은 대역의 네트워크에 컨테이너가 구성되었기 때문에 통신이 서로 가능함. 
  
- 만약 커스터마이징한 별도의 `bridge` 네트워크를 구축해서 컨테이너를 구성한다면 기존의 `docker0`에 구성된 컨테이너와는 통신이 불가능

```shell
# 순차 실행 시 부여된 IP확인 가능
sudo docker container run -d -p 80:80 --name webserver1 httpd
sudo docker container exec -it webserver1 /bin/bash
sudo apt-get update
sudo apt-get install net-tools
ifconfig
```
![docker_network3](https://yunsikus.github.io/assets/img/post_img/docker_network3.jpg) 

### 2. `host`

![docker_network4](https://yunsikus.github.io/assets/img/post_img/docker_network4.jpg) 

- `host` 네트워크는 단어 그대로 별도의 경유 없이 도커 엔진이 작동하고 있는 Host의 IP주소를 그대로 사용하는 것을 뜻함. 

- `bridge`를 통해 NAPT의 과정을 거치지 않아도 되며, 명시적으로 컨테이너의 포트를 명령어에 작성하지 않아도 됨. 

- 다시 말해, 컨테이너를 격리된 네트워크로 구성하지 않고 도커 엔진과 한 공간에 두는 것

- 리눅스만 지원
  
- 가상머신의 IP와 컨테이의 IP가 동일

### 3. `None`

- `none`은 말 그대로 컨테이너를 격리된 공간에 차단시켜놓고 어떠한 네트워크와도 연결하지 못하게 하는 것을 가리킴

---


## 3. 도커 네트워크 명령어

### 1) `ls` - 네트워크 목록 조회

```shell
sudo docker network ls 
sud docker network ls --filter driver=bridge
```

![docker_network5](https://yunsikus.github.io/assets/img/post_img/docker_network5.jpg) 

- `--filter` 를 통해 특정 조건에 부합하는 요소를 조회할 수 있다. 
- 아래와 같이 네트워크의 종류를 설정하여 조회할 수도 있으며 name, scope등도 조건으로 설정할 수 있다.

![docker_network6](https://yunsikus.github.io/assets/img/post_img/docker_network6.jpg) 

### 2) `create` - 네트워크 생성

```shell
sudo docker network create --driver=bridge new-bridge
```
![docker_network7](https://yunsikus.github.io/assets/img/post_img/docker_network7.jpg) 


- 기본적으로 제공되는 네트워크 외에 새로운 네트워크를 구성하기 위해서는 위와 같이 명령어를 작성
- 보통은 명령어를 입력해서 생성하기 보다는 뒤에 이어질 docker-compose.yaml 내에 세팅하는 경우가 더 많음

### 3) `connect` - 컨테이너를 네트워크에 연결

```shell
sudo docker network connect new-bridge webserver1
sudo docker container inspect webserver1
```
![docker_network8](https://yunsikus.github.io/assets/img/post_img/docker_network8.jpg) 

- 기존에 컨테이너 생성 시 디폴트로 연결되어 있는 bridge 네트워크는 유지되면서 새롭게 new-bridge와 연결됨. 즉 하나의 컨테이너가 두 개의 네트워크에서 각각 공유될 수 있음
- 두 네트워크는 게이트웨이가 각각 다르다. 즉, 컨테이너 입장에서는 두 가지 대역과 모두 통신이 가능한 상태가 됨

### 4) `disconnect` - 컨테이너를 네트워크에서 해제

```shell
sudo docker network disconnect new-bridge webserver1
```

### 5) `inspect` - 네트워크 상세 정보 조회

```shell
sudo docker network inspect new-bridge
```

### 6) `rm` - 네트워크 삭제

```shell
sudo docker network rm new-bridge
```


## Reference
- 미즈구치 카츠야 저/이승룡 역, 『모두의 네트워크』, 길벗(2020)
- 고재성, 이상훈, 『IT 엔지니어를 위한 네트워크 입문』, 길벗(2020)
- Charles Severance, 『Introduction to Networking: How the Internet Works』, CreateSpace Independent Publishing Platform; 1 edition (May 29, 2015)
- 멋직 도커/쿠버니테스 온라인 부트캠프 1기 강의 정리