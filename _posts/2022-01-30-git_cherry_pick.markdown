---  
layout: post
title: "[Git] 다른 브랜치에 있는 커밋을 내 브랜치로 가져오고 싶을 때 - cherry pick"
subtitle: "[Git] 다른 브랜치에 있는 커밋을 내 브랜치로 가져오고 싶을 때 - cherry pick"  
categories: Development
tags: Git
comments: true  
---  
## 테스트를 위한 파일 생성
- `master` 브랜치 
  
```python
# a.py
a파일 생성
```

```python
# b.py
b파일 생성
```

- `cherry pick` 브랜치
  
```python
# a.py
a파일 수정
```


## cherry pick

- `git cherry-pick {커밋 ID}`은 다른 브랜치의 일부 커밋을 현재 브랜치로 가지고 온다. 
- cherry-pick 브랜치에서 커밋 ID를 확인 후에 다시 master 브랜치 이동 후 가져온다. 
   

![git_cherry_pick1.jpg](https://yunsikus.github.io/assets/img/post_img/git_cherry_pick1.jpg)