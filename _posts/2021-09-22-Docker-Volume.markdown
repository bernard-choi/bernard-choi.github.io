---
layout: post
title: "[Docker] Docker Volume" 
subtitle: "[Docker] Docker Volume"
categories: Development
tags: Docker
comments: true


---
## 1. 도커 볼륨 

- 컨테이너는 언제든지 멈추고 삭제할 수 있습니다. 컨테이너 내부의 디스크에서 존재하던 파일 혹은 데이터 역시 컨테이너를 삭제하게 되면 함께 사라집니다.
- 영구적으로 혹은 일정 기간동안 데이터를 보관하는 용도로 사용해야 한다면 이를 관리하기 위해 다른 방법이 필요
- 도커에서는 `Volume`과 `Bind Mount`라는 개념으로 Host의 일부 영역을 할당해 스토리지로 사용할 수 있게 해준다. 두 개념을 비교해보자

![docker_volume1](https://yunsikus.github.io/assets/img/post_img/docker-volume1.jpg) 

## 2. Bind Mount

- `Bind Mount`는 초기 도커에서 사용된 스토리지 기능으로, Host 경로 일부를 설정해 컨테이너에 마운트하는 기능을 가리킨다.
- 쉽게 말해 '/Desktop' 이라는 경로를 USB 삼아 컨테이너에 연결하는 것과 같다.
-  사용하는 방법은 컨테이너를 실행할 당시에 —v 옵션을 작성해주는 것.
- 호스트의 `/home/ubuntu/bindmount-test` , `/var/lib/docker/volumes/<해시값>`의 경로가 컨테이너의 `/bindmount-test`와 연결됨. 
- 이렇게 연결된 경로는 파일을 복사하는 개념이 아니라 완전히 같은 디렉토리로 취급됨. 

```shell
mkdir /home/ubuntu/bindmount-test
cd /home/ubuntu/bindmount-test
touch bindmount.txt
echo "bind-mount" > bindmount.txt

sudo docker container run -d -it --name bindmount -v /home/ubuntu/bindmount-test:/bindmount-test ubuntu:18.04
sudo docker container run -v [호스트 경로]:[컨테이너 경로] [이미지명:태그]
```

![docker_volume2](https://yunsikus.github.io/assets/img/post_img/docker-volume2.jpg) 

## 3. Volume

- `Volume`은 `Bind Mount` 이후에 나온 개념으로, 호스트의 경로를 임의로 지정할 수 없습니다. 대신 도커 엔진에서 관리하는 `/var/lib/docker/volumes/<볼륨명>` 으로만 세팅이 됩니다. 
- 볼륨은 컨테이너를 실행하는 단계에서 생성할 수 있으며 볼륨만 개별적으로 생성하는 것도 가능합니다.

```shell
~$ sudo docker container run -d -it --name volume -v volume1:/volume-test ubuntu:18.04
~$ sudo docker container attach volume # 컨테이너 접속

/# echo "volume" > ./volume-test/volume.txt # 볼륨 연결한 경로에 txt 파일 생성

~$ cd /var/lib/docker/volumes/volume1/_data # 컨테이너에서 빠져나온 후 볼륨 경로에서 확인해보니
~$ cat volume.txt # 컨테이너에서 생성했던 txt 파일 확인 가능
```

![docker_volume3](https://yunsikus.github.io/assets/img/post_img/docker-volume3.jpg) 

![docker_volume4](https://yunsikus.github.io/assets/img/post_img/docker-volume4.jpg) 

- 해당 경로를 확인해보면 컨테이너 생성 시 지정했던 볼륨명인 volume1 이름의 디렉토리가 생성된 것을 확인할 수 있다. 
- 볼륨을 통해 저장된 데이터는 `/var/lib/docker/volumes/[볼륨명]/_data` 에 저장되기 때문에 컨테이너에서 입력했던 문자열 텍스트 파일이 호스트에서도 동일한 내용으로 조회된다. 

## Reference

- 미즈구치 카츠야 저/이승룡 역, 『모두의 네트워크』, 길벗(2020)
- 고재성, 이상훈, 『IT 엔지니어를 위한 네트워크 입문』, 길벗(2020)
- Charles Severance, 『Introduction to Networking: How the Internet Works』, CreateSpace Independent Publishing Platform; 1 edition (May 29, 2015)
- 멋직 도커/쿠버니테스 온라인 부트캠프 1기 강의 정리