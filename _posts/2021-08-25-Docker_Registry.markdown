---
layout: post
title: "[Docker] Dockerfile Registry - DockerHub, Local로 구축하기" 
subtitle: "[Docker] Dockerfile Registry- DockerHub, Local로 구축하기"
categories: Development
tags: Docker
comments: true


---

## Docker Registry

- 도커 레지스트리는 도커 이미지들을 모아 놓고 필요할 때 사용할 수 있도록 구축한 저장소. 
- 레지스트리를 통하여 이미지를 공유하는 여러 가지 방법은 크게 3가지가 있다. 
  - dockerhub
  - 직접 컨테이너에 레지스트리 구축
  - 클라우드 사업자에서 제공하는 레지스트고

## Registry를 이용하는 이유

- **보안문제**
  - 이미지 내부에 보안상 중요한 파일이나 내용이 필요한 경우에는 더욱 엄격히 관리할 필요가 있다. 
  - 이미지 자체도 암호화 되어야 하고 레지스트리에 접근할 수 있는 권한도 통제해야 한다. 
  - 이런 일련의 것들을 IAM라는 형태로 제공

- **배포 파이프라인 효율화**
  - 레지스트리에 저장된 이미지는 단순히 저장에서 끝나는 것이 아니라 CI/CD라는 과정까지 연속적으로 활용됨. 
  - CI(Continuous Integration): 지속적 통합
    - 빌드/테스트가 자동으로 수행된 이후 개발자의 수정 사항이 중앙 저장소에 머지되는 것 까지의 과정을 일련의 자동화 과정 및 개발 문화가 포괄
  - CD(Continuous Delivery): 지속적 전달
    - 운영 환경에 배포할 소스코드가 자동으로 세팅
    - 빌드 이후의 변경 사항을 테스트 및 운영 서버에 자동으로 배포
    - 필요에 따라 테스트 서버를 동적으로 생성할 수 있음
  
## DockerHub

- 도커 허브는 도커에서 디폴트로 참조하는 레지스트리로서 각종 공식 이미지를 손쉽게 사용할 수 있도록 구성되어 있습니다. 또한 무료 계정을 생성하여 개인이 빌드한 이미지도 공개할 수 있습니다.  

- 실습 전 계정 생성을 먼저 진행합니다. 

사용 방법은 다음의 과정을 따릅니다. 

1) 저장소 만들기

- 'Create Repository' 버튼을 눌러 저장소로 만들어줍니다. 

![registry1](https://yunsikus.github.io/assets/img/post_img/registry1.jpg) 

- 저장소명은 용도에 맞게 자유롭게 입력해주면 됩니다.(예제의 경우 'portpolio'로 설정)
-  Visibility 항목에서는 도커 허브에서 내 저장소가 검색 결과에 조회되는가에 대해 Public과 Private 설정을 할 수 있습니다. 

- 무료계정의 경우 하나의 저장소만 Private으로 설정할 수 있습니다. 

![registry2](https://yunsikus.github.io/assets/img/post_img/registry2.jpg) 

- 저장소가 성공적으로 생성되었다면 위와 같은 화면이 나오게 됩니다.
- 오른쪼 하단의 'Automated Builds'라는 항목이 있는데 이는 Github나 Bitbuket에 push한 소스를 기반으로 자동으로 이미지를 빌드해주는 기능입니다. 최근에 유료로 변경됨. 

1) 이미지 빌드

```shell
sudo docker build -t <계정명>/<저장소명>:[태그명] .
sudo docker build -t gafield8785/portfolio:1.0 .
```

3) 도커 허브 계정 로그인

- 명령어를 입력하고 username과 password를 차례로 입력해준다. 

```shell
sudo docker login
```

4) 도커 허브 이미지 공유

```shell
sudo docker <계정명>/<저장소명>:[태그명]
sudo docker push gafield8785/portfolio:1.0
```
- 이미지 명명 규칙 및 계정정보에 이상이 없다면 정상적으로 이미지가 공유됩니다. 

![registry3](https://yunsikus.github.io/assets/img/post_img/registry3.jpg) 

## 로컬 레지스트리 구축
- 도커를 통해 실행되는 어플리케이션은 컨테이너 단위로 실행됩니다. 이미지가 저장되는 레지스트리 역시 컨테이너 상에 구축할 수 있는데 이는 도커사에서 Regisry라는 이름으로 제공하고 있습니다. 

1) Registry 컨테이너 실행

```shell
sudo docker run -d -p 5000:5000 --restart always --name registry registry2
# --restart always의 경우 도커 엔진이 재시작되는 경우 자동으로 컨테이너를 재시작하는 옵션
```

2) 이미지 태깅
- 컨테이너에 구축한 레즈스트리에 이미지를 공유하는 경우 `<컨테이너 IP>:<포트>/<이미지명>` 의 양식을 반드시 지켜야 합니다.
- 포트의 경우 컨테이너를 실행할 당시 5000으로 지정이 되어 있었으나 구성 상 변경이 필요한 경우 태그를 설정할 떄도 반영해주어야 합니다. 
  
```shell
sudo docker tag <기존 이미지명>:[태그명] <레지스트리 컨테이너 IP>/<이미지명>:[태그명]
sudo docker tag gafield8785/portfolio:1.0 localhost:5000/portfolio:1.0
```

3) 이미지 공유
- 명령을 실행하면 도커허브와는 다른 경로로 이미지가 공유된 것을 확인할 수 있습니다. 

```shell
sudo docker push localhost:5000/portfolio:1.0
```

