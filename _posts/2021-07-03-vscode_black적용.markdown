---  
layout: post
title: "[VSCODE] Python Formatter - Black 적용하기"
subtitle: "[VSCODE] Python Formatter - Black 적용하기"  
categories: Development
tags: 개발환경 
comments: true  
--- 

##  black이란?

`Black`은 최근 파이썬 커뮤니티에서 쓰이고 있는 코드 포매터. 
기존 코드 포맷터와 달리 `Black`은 설정의 여지가 거의 없어서 정해놓은 특정 포맷팅 규칙을 따라야함. 
이러한 특징은, 개발자간의 코드 스타일을 협의하는 과정을 줄여준다. 수많은 오픈 소스 파이썬 프로젝트들과 파이썬을 사용하는 수많은 기업들에서 `Black`을 정식 코드 포맷터로 채택해서 사용한다. 

## 적용 방법

- black 파이썬 패키지 설치
```python
pip install black
```

- `ctrl` + `,` 로 세팅창 들어가기
- format on save 체크 (저장 시 포맷 적용)
- python formatting provider를 `black`으로 지정