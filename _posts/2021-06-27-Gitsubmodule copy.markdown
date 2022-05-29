---  
layout: post
title: "[Git] Gitsubmodule"
subtitle: "[Git] Gitsubmodule"  
categories: Development
tags: Git
comments: true  
---  
## 서브모듈이란?
깃의 서브모듈은 깃 레포 아래에 다른 하위 깃 레포를 관리하기 위한 도구


## Gitsubmodule 추가 방법

- 아직 parent에 submodule이 추가되지 않은 상태에서 다음 명령어를 입력하여 submodule 추가 해줄 수 있다.

- submodule_test_child는 하위 깃 레포를 포함하고 있는 디렉토리지만 깃에서는 서브모듈 정보를 포함하고 있는 파일처럼 관리한다.  

```shell
git submodule add git@github.com:snowdeer/child.git child
git commit -m "submodule is added."
git push
```

## 부모 프로젝트에서 자식 프로젝트의 내용 변경하고 업데이트하기

```shell
# 자식 프로젝트 내용 변경되었는지 확인
git status
# 변경사항 커밋 후 푸시
git commit -am 'Update submodule'
git push origin master
```


## git clone으로 parent를 가져왔을 때

git clone으로 parent를 가져왔을 때, 내부의 child는 디렉토리만 만들어져 있고 내부가 없다. 이때 submodule 초기화 및 업데이트를 해주어야 한다. 루트에서 다음 명령어를 실행합니다.

```shell
git submodule init
git submodule update
git submodule foreach git checkout master
(각각의 sub project를 master branch로 check 하기 위한 명령어)
(submodule update로 데이터를 받으면 sub project는 detached HEAD 상태로 어떤 branch 에도 속하지 않기 때문)
```

- 다만, 이때 submodule의 소스 버전은 최신 버전을 가리키는 것이 아니라, submodule add를 수행했을 떄의 버전을 가리키고 있다.
- submodule은 레포가 실제로는 분리되어 있기 때문에 각 모듈의 버전이 따로 관리되는데, parent 프로젝트에서는 현재 submodule의 버전이 최신인지 아닌지 신경쓸 필요없이 안정적인 특정 버전만 가리키면 되기 때문에 프로젝트 배포 등에서는 관리가 수월한 장점이 있다.
- 물론 개발중인 프로젝트에서는 각 submodule들을 최신 버전으로 유지해야 할 경우 각 submodule들의 업데이트를 수동으로 한번씩 더 해줘야 하는 단점이 있다.

## submodule 업데이트 하고 merge하기

```shell
git submodule update --remote --merge
git submodule update --remote <REMOTE-REPONAME> --merge
(특정 submodule만 업데이트 하고 싶다면)
```
## submodule 최신 버전으로 교체

 submodule을 최신 버전으로 교체하는 방법

 - 1) `child` 디렉토리에 들어가서 각각의 submodule들을 개별 업데이트 해주는 방식. 각 submodule들 디렉토리에서 `git pull` 명령어나 `git checkout` 명령어 등을 이용해서 업데이트 가능

 - 2) `parent` 내에서 `git submodule foreach git pull origin master` 명령어를 실행하여 하위 submodule들을 전부 업데이트

## git submodule 삭제

```shell
1. 해당 모듈 내용 삭제
vim .gitmodules

2. cache 삭제
git rm --cached <submodule name>
(에러날경우 git submodule deinit <submodule> 먼저 실행)

3. submodule 삭제
rm -rf .git/modules/<submodule>

4. 최종삭제
rm -rf <submodule>

5. commit & push
```

## Reference

-[https://snowdeer.github.io/git/2018/07/15/how-to-use-git-submodule/](https://snowdeer.github.io/git/2018/07/15/how-to-use-git-submodule/)
-[https://ohgyun.com/711](https://ohgyun.com/711)
