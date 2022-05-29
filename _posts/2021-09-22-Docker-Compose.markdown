---
layout: post
title: "[Docker] Docker Compose" 
subtitle: "[Docker] Docker Compose"
categories: Development
tags: Docker
comments: true


---
## 1. Docker Compose란? 

- 멀티 컨테이너를 구동하기 위하여 나타난 도커 컴포넌트중 하나
- 여러개의 컨테이너를 정의하고 실행하는 역할을 한다.
- docker-compose.yml 이라는 파일을 생성하고 컨테이너들의 설정값을 담는다. 

## 2. Docker Compose 명령어

### 1) `up` - 멀티 컨테이너 생성 및 실행
- 컨테이너의 이름은 별도로 설정하지 않으면 `[docker-compose.yml 파일이 위치한 디렉토리명]_[서비스명]_[번호]` 의 형태로 정의된다.


<details>
<summary>옵션</summary>
<div markdown="1">       

`-d`, `--detach` : 컨테이너를 백그라운드에서 실행
`--build` : 컨테이너를 생성하기 전에 이미지를 빌드
`--no-build` : 실행 대상 이미지가 존재하지 않더라도 빌드하지 않는다. 
`--abort-on-container-exit` : 여러 컨테이너들 중 하나라도 종료되면 모두 종료된다. `--detach`와 함께 사용할 수 없다. 

</div>
</details>

```shell
sudo docker-compose up
```

### 2) `ps` - 컨테이너 조회
- `docker ps` 혹은 `docker container ls`를 사용해도 실행중인 컨테이너를 조회할 수 있다. 다만, `docker-compose ps`를 통해 조회했을 때의 출력 양식에서 약간 차이가 난다. 

<details>
<summary>옵션</summary>
<div markdown="1">       

`-q, --quiet` : 컨테이너 ID만 출력
`--sercices` : 정의된 서비스 명을 출력
`-a, --all` : 종료된 컨테이너를 포함하여 모든 컨테이너를 출력

</div>
</details>

```shell
sudo docker-compose ps
```

![docker_compose1](https://yunsikus.github.io/assets/img/post_img/docker-compose1.jpg) 

### 3) `run` - 컨테이너 내부에서 명령 실행
- run 명령어 인수를 컨테이너명이 아닌 docker-compose.yml에 정의된 서비스명으로 입력해야 함.

```shell
sudo docker-compose run [서비스명] [실행 대상 명령]
sudo docker-compose run db bash
```

![docker_compose2](https://yunsikus.github.io/assets/img/post_img/docker-compose2.jpg)

### 4) `start` - 생성되어 있는 컨테이너 실행
```shell
sudo docker-compose start
```

![docker_compose3](https://yunsikus.github.io/assets/img/post_img/docker-compose3.jpg)

### 5) `stop` - 생성되어 있는 컨테이너 종료
```shell
sudo docker-compose stop
```

![docker_compose4](https://yunsikus.github.io/assets/img/post_img/docker-compose4.jpg)

### 6) `down` - 컨테이너 종료 및 삭제

![docker_compose5](https://yunsikus.github.io/assets/img/post_img/docker-compose5.jpg)

---

## 3. docker-compose.yml 작성

### 1) `version`
- 최상단에 버전을 정의해야함. 
- 각 버전별로 명령어 혹은 표기법이 다르기 때문에 정상적으로 작동하지 않는 경우 버전을 체크해야함.

```yaml
version: "3.9"
```

### 2) `services`

- 서비스는 Compose에서 실행할 컨테이너입니다. 
- 각 서비스 별로 그에 맞는 환경의 컨테이너를 구성하기 위해 내부에 다양한 옵션을 추가한다. 
- 혼동하지 말아야 하는 것은 서비스명과 컨테이너명은 다른 개념이다. 
- 만약 원하는 컨테이너명을 설정하고 싶다면 각 서비스 하위에 `container_name` 키와 설정하려는 값을 추가하면 된다. 
  
```yaml
services:
  db:
  ...
  nc:
  ...
```

### 3) `image`

- 말 그대로 컨테이너로 실행할 대상 이미지를 설정하는 것. 
- `Dockerfile`에서 `FROM`을 통해 베이스 이미지를 설정하는 것봐 비슷함.

```yaml
services:
  db:
    image: postgres:alpine
...
  nc:
    image: nextcloud:apache
...
```

### 4) `build`

- 일반적으로 작성한 `Dockerfile`에서 빌드된 이미지를 기반으로 컨테이너를 실행
- 실행할 이미지명 대신에 빌드할 `Dockerfile`의 정보를 입력하여 이를 빌드하고 바로 이미지로 사용이 가능
- `dockerfile` 옵션을 사용하면 파일의 이름이 `Dockerfile`이 아닌 것도 빌드 대상으로 지정할 수 있다. 
- 경로 역시 동일 경로가 아닌 다른 경로를 지정할 수 있다.

```yaml
services:
  db:
...
  nc:
    build:
      context: .
      dockerfile: Dockerfile
...
```

### 5) `command`
- 생성된 컨테이너에 어떤 명령을 내릴지 세팅
- 보통 컴파일러나 특정 언어로 작성된 어플리케이션을 명령어로 실행해야 하는 경우에 사용됨

```yaml
services:
  db:
...
  nc:
    build:
      context: .
      dockerfile: Dockerfile
      command: java -jar app.jar
```

### 6) `ports`

- 포트포워딩을 설정하는 항목으로 `docker run -p 80:80` 와 동일한 기능을 한다.
- 다만, yaml파일에서 `XX:YY`의 형식이 시간값으로 해석될 수 있기 때문에 안전하게 따옴표 처리를 하는것을 권장

```yaml
services:
  db:
...
  nc:
    ports: "80:80"
...
```

### 7) `depends_on`

- 특정 서비스가 먼저 시작되면 이어서 시작할 수 있도록 설정하는 명령어
- `nc`라는 서비스에 `db`가 `depends_on`으로 걸려있는데, 이는 `db`서비스가 시작되면 `nc`서비스가 시작되도록 순서를 정하는 것. 
- 다만, `db`가 완전히 초기화되어 리스닝 상태까지 도달했는지는 확인하지 않는다. 단순히 시작이 되었느냐, 아니냐만을 가지고 서비스를 시작하기 때문에 `restart` 명령어를 추가로 넣어줘야함.

```yaml
services:
  db:
...
  nc:
    depends_on:
      - db
...
```

### 8) `environment`

- 환경변수를 설정하는 항목이며, DB계정 및 초기 DB세팅등에 주로 사용됨. 
- 이외 필요에 따라 각 컨테이너별 환경변수를 할당할 수 있다. 
- `docker run -e`와 유사한 기능이라고 볼 수 있다.

```yaml
services:
  db:
...
  nc:
    environment:
      - POSTGRES_HOST=db
      - POSTGRES_PASSWORD=nextcloud
      - POSTGRES_DB=nextcloud
      - POSTGRES_USER=nextcloud
...
```

### 9) `volumes`

- 볼륨을 세팅하는 항목이며, `docker run -v`와 같은 역할을 한다. 
- 컨테이너가 삭제되어도 데이터 유실이 되지 않도록 호스트의 일부 영역을 할당하게 되며 `[볼륨명]:[할당할 호스트 경로]`를 작성하면 된다. 
- 더불어 `services`와 같은 위계에 `volumes`를 작성하고 그 하위에 서비스에 설정한 볼륨명을 작성해주면 된다. 

```yaml
services:
  db:
    volumes:
      - db_data:/var/lib/postgresql/data
...
  nc:
    volumes:
      - nc_data:/var/www/html
...
volumes:
  nc_data: 
  db_data:
...
```

### 10) `restart`
- 서비스 재시작을 설정할 수 있다. 기본값은 재시작을 하지 않는 것이지만 웹서비스의 경우 재시작을 `always`로 설정하는 경우가 많다.
- 이는 `db`가 완전히 리스닝이 가능한 상태가 되기 전에 웹 서비스에서 Connection을 생성하려는 경우 에러가 발생해 서비스가 비정상 종료가 되기 때문. 
- 재시작을 하다가 `db`가 Connection 생성이 가능한 상태가 되면 웹 서비스가 정상적으로 실행됨

```yaml
services:
  db:
...
  nc:
...
    restart: always
...
```

### 11) `expose`

- `Dockerfile`에도 `EXPOSE`는 실제로 포트포워딩을 수행하지 않고 명시하는 역할만 하였음
- `docker-compose.yml`에서는 호스트 OS에서의 접근은 불가능하고 연결된 타 컨테이너와의 통신만 가능. 

```yaml
services:
  db:
...
    expose:
      - 5432
...
  nc:
...
```