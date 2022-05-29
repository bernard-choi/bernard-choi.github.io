---
layout: post
title: "[Docker] Dockerfile 명령어"
subtitle: "[Docker] Dockerfile 명령어"
categories: Development
tags: Docker
comments: true


---

## FROM - 베이스 이미지 설정

```shell
FROM ubuntu:18.04
```
- 베이스 이미지는 세팅하는 명령으로서 기본적으로 Docker Hub 에서 이미지를 탐색해 빌드에 사용

## RUN - 이미지를 빌드할 때 실행할 명령 설정

```shell
RUN apt-get -y update                           # Shell 형식
RUN ["/bin/bash", "-c", "apt-get -y update"]    # Exec 형식
```

- `RUN`을 통해 작동하는 명령은 아직 컨테이너가 작동하는 상태가 아니다. 
- 베이스 이미지에 추가적으로 명령을 실행해 필요한 패키지나 미들웨어를 설치하기 위해 많이 사용
- Shell 형식에서 `RUN` 뒤에 바로 명령어를 작성하는 것은 `/bin/sh -c`이 붙어있는 것과 마찬가지
- 두 가지가 혼용되는 경우가 많지만 실행할 쉘 혹은 프로그램을 지정한다는 측면에서 **Exec 형식**이 권장됨
 

## CMD - 이미지를 통해 생성된 컨테이너 내부에서 실행되는 명령

```shell
FROM ubuntu:18.04

CMD echo "Hello, Docker!"             # Shell 형식
CMD ["/bin/echo", "Hello, Docker!"]   # Exec 형식
```
- 컨테이너는 `docker container run` 명령어를 통해 실행됨. 
- 이렇게 컨테이너가 실행될 때 수행할 명령은 `CMD`를 통해 세팅이 가능.
- 유의할 점은 Dockerfile 전체에서 `CMD`명령은 단 하나만 유효하고 여러개의 명령이 있다면 마지막 것만 실행이 된다. 
- `CMD` 명령 역시 `RUN`과 같이 Shell, Exec 형식 모두 사용이 가능

## ENTRYPOINT - 이미지를 통해 생성된 컨테이너 내부에서 실행되는 명령
```shell
FROM ubuntu:18.04

ENTRYPOINT echo "Hello, Docker!"             # Shell 형식
ENTRYPOINT ["/bin/echo", "Hello, Docker!"]   # Exec 형식
```
- 역할이 `CMD` 명령어와 비슷
- `ENTRYPOINT`의 명령은 사용자가 어떤 인수를 명령으로 넘기더라도 Dockerfile에 명시된 명령을 그대로 실행
- 반명 `CMD`는 컨테이너를 실행할 때 사용자가 인수를 넘기면 기존에 작성된 내용을 덮어 쓴다. 
- 이러한 특성을 이해한다면 좀 더 효율적인 이미지 생성이 가능

## CMD와 ENTRYPOINT의 차이는?

예시를 통해 살펴보자. 
```shell
FROM ubuntu:18.04

ENTRYPOINT ["top"]
CMD ["-d", "5"]
```
위의 dockerfile은 컨테이너가 뜰 시 top명령어를 실행한다. 이미지 빌드 후 컨테이너를 띄운다면 5초마다 top 명령어가 실행될 것이다. 

```shell
docker build -t top . # top이라는 이미지를 생성

docker container run -it top # 5초마다 top가 갱신됨
docker container run -it top -d 10 # 10초마다 top가 생신됨
```
`ENTRYPOINT`는 사용자가 인수를 넘기는 것과 상관없이 무조건 실행되는 부분이고 `CMD`는 인수를 통해 덮어 쓸 수 있다. 

## ONBUILD - 이미지 빌드가 완료된 후에 실행되는 명령
```shell
FROM ubuntu:18.04

ONBUILD RUN ["apt-get", "update"]
ONBUILD RUN ["apt-get", "-y", "install", "vim"]
```

```shell
sudo docker image build -f ./Dockerfile.base ./ -t baseimage # Parent Image
```
- Parent 이미지를 생성할때는 아무일도 일어나지 않는다. 

```shell
# ./docker/Dockerfile
FROM baseimage
```

```shell
sudo docker image build -t hellodocker-child . # Child Image
```
- `ONBUILD`가 작성되어 있는 Dockerfile을 빌드해서 생성된 이미지를 베이스로 하여 자식 이미지를 생성할 때 그 명령이 실행.
- 위와 같이 Dockerfile을 작성해서 베이스 이미지를 빌드해 놓으면 모든 이미지에 공통적으로 설치되어야 하는 패키지나 실행명령을 빌드 당시에 적용 가능

## HEALTHCHECK - 컨테이너의 작동상태 체크

```shell
FROM httpd

RUN ["apt-get", "update"]
RUN ["apt-get", "-y", "install", "curl"]

HEALTHCHECK --interval=3s --timeout=5s --retries=3 CMD curl --fail http://localhost:80/ || exit 1
```
- Dockerfile을 작성할떄 `HEALTHCHECK` 명령을 추가하면 이를 컨테이너 내부에 로그로 남길 수 있습니다. 
- 컨테이너에서 이 명령어가 잘 작동하는지는 `docker ps`  명령어를 통해 `STATUS` 항목을 살펴보면 됩니다. 
- 구동시간 옆에 healthy라는 문구가 끕니다. 
- 더 정확하게 로그를 보려면 `docker container inspect --format="{{json .State.Health }}"  <컨테이너명>`을 통해 "Health"에 해당하는 값을 확인하면 됩니다. 

## ENV - 환경변수 설정

## WORKDIR - 작업 디렉토리 할당

```shell
FROM ubuntu:18.04

RUN mkdir /keypair
ENV KEY /keypair

WORKDIR $KEY
RUN echo "dummy key" > key.txt
```
```shell
sudo docker image build -t key-setting .
sudo docker container run -it --name=key-setting key-setting
```
-  `ENV` 에서 설정된 환경변수는 `RUN`, `CMD`, `ENTRYPOINT` 명령에서 모두 사용 가능하다. 
-  `WORKDIR`는 리눅스의 `cd`와 유사. Dockerfile 내부에서 경로를 이동할떄도 쓰일 뿐만 아니라, 이렇게 빌드된 이미지를 대화형 컨테이너로 실행할 때 프롬프트의 최초위치를 결정하기도 한다. 

## USER - 유저할당

## LABEL - 이미지 버전 정보, 작성자 등 레이블 정보 등록

## ARG - Dockerfile 내부의 변수 할당

```shell
FROM ubuntu:18.04

ARG newuser=gginmin

LABEL version="1.0"
LABEL description="custom user test"

RUN useradd $newuser

USER $newuser
```

- 기본적으로 컨테이너를 실행하면 `root` 사용자로 로그온이 됩니다. 하지만 보안 혹은 기타 접근 제한을 위해 로그온 계정을 이미지 생성 단계에서 설정해야 하는 경우 `USER` 명령어를 활용합니다. 
- `LABEL` 은 각종 메타데이터를 이미지에 기록하는 역할을 한다. `docker image inspect` 명령어를 실행한 뒤 Labels 라는 키 항목에서 확인이 가능하다. 
- `ARG`는 빌드가 되는 시점에만 유효한 변수를 할당한다. `ENV` 명령어로 할당된 변수는 컨테이너가 구동된 후에도 유효하지만 `ARG`는 `build` 명령어를 실행할 때만 작동한다. 



## EXPOSE - 포트 번호 설정
```shell
FROM ubuntu:18.04

RUN apt-get -y update && apt-get -y upgrade
RUN apt-get -y install nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

- 이미지에 포트를 작성하면 따로 컨테이너 실행 시에 `-p`옵션으로 포트를 지정안해도 될까?
- 이미지에 작성된 `EXPOSE` 명령어는 단순히 문서의 성격만 가짐. 실제로 호스트에서 컨테이너의 포트와 통신하도록 listening 상태를 만들어주지 않습니다. 
- 개발자에게 어떤 포트로 어떤 방식을 이용해야 하는지 알려줄 뿐입니다. 컨테이너 호스트의 통신 요청에 응답하기 위해서는 반드시 `container run` 단계에서 `-p` 옵션을 통해 포트를 설정해주어야 합니다. 

## ADD - 파일 복사(URL 포함)
## COPY - 파일 복사

```shell
FROM ubuntu:18.04

RUN ["apt-get", "update"]
RUN ["apt-get", "-y", "install", "curl"]

RUN mkdir /addmyfiles
RUN mkdir /copymyfiles

# ./docker 디렉토리에 add.html, copy.html 파일을 미리 생성
ADD add.html /addmyfiles
COPY copy.html /copymyfiles
```

- `ADD` 와 `COPY`는 Dockerfile이 위치한 경로에 있는 파일을 이미지로 복사해오는 명령어
- 겉보기에는 기능상에 큰 차이가 없으나 `ADD`는 추가적으로 두 가지의 기능을 가지고 있다. 
  - 1. URL을 통한 파일 복사(웹상에 있는 파일도 추가 가능)
  - 2. 로컬에서 압출파일 복사 시 헤재하여 복사
      - 웹에서 받은 압출파일은 압축해제만 되고 tar 형태는 유지 됨
      - 그 외의 경우 압축을 자동으로 해제하기 떄문에 `tar`나 `tar.gz`을 복사하는 경우 유의해야 함 
    