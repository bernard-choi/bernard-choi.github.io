---  
layout: post
title: "[Git] amend commit & rebase로 이전에 쌓인 커밋들을 변경하자"
subtitle: "[Git] amend commit & rebase로 이전에 쌓인 커밋들을 변경하자"  
categories: Development
tags: Git
comments: true  
---  
## 테스트를 위한 파일 생성
```python
# a.py
a파일 생성
```

```python
# b.py
b파일 생성
```

```python
# c.py
c파일 생성
```

## 현재 작업 중인 커밋(HEAD) 간단하게 수정할 때

- `git commit --amend`를 사용
- 현재 작업중인 커밋에서 `c.py`를 추가 수정함

```python
# c.py
c 파일을 생성
c 파일을 수정 # 추가
```

- `git add .` 후에 `git commit --amend`로 커밋 메시지까지 수정(c 파일 생성 -> c 파일 수정)
  
![git_amend2.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend2.jpg)

- `git show` 시 다음과 같이 현재 커밋 수정된 것 확인

![git_amend1.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend1.jpg)

- 커밋 메시지의 수정을 필요로 하지 않는 경우 `--no-edit` 옵션을 붙이면 된다.

## 아래에 있는 커밋들 중 일부를 수정하거나 변경할 때

- `git rebase --interactive`를 사용
- 일일이 HEAD 커밋을 바꿔주고 `git commit --amend`로 바꿔줄수도 있으나 `git rebase --interactive`로 한번에 변경 가능

- `git log --oneline`로 현재 커밋 확인

![git_amend3.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend3.jpg)

- `git rebase --interactive {커밋 ID} or root`
- 이전 커밋과 이전전 커밋 수정을 위해 `pick`을 `edit`로 수정
- 대표적인 COMMAND는 다음과 같다.
  - `pick` : 별다른 변경 사항없이 그냥 커밋으로 두겠다.
  - `edit` : 해당 커밋 내용을 변경할 것이며 커밋 메시지도 변경할 수 있게 하겠다.
  - `reword` : 해당 커밋의 메세지만 변경하겠다.
  - `drop` : 해당 커밋을 제거하겠다. 

![git_amend4.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend4.jpg)

- 저장 후 변경하려는 커밋으로 자동으로 이동함. 
- 변경 사항 추가 후 `git commit --amend` 입력

![git_amend5.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend5.jpg)

- `git rebase --continue` 로 다음 수정이 필요한 커밋으로 이동

![git_amend6.jpg](https://yunsikus.github.io/assets/img/post_img/git_amend6.jpg)

- 다시 마찬가지로 변경 사항 추가 후 `git commit --amend`를 입력