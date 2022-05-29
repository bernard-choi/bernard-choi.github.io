---  
layout: post
title: "[VSCODE] VSCODE에 Vim extension 적용하기"
subtitle: "[VSCODE] VSCODE에 Vim extension 적용하기"  
categories: Development
tags: 개발환경
comments: true  
--- 

1) Vscode의 extension 설치

- extention에서 vim extension 설치

2) settings 설정 변경

- vimrc 검색 후 Enable
- vimrc path 설정(/usr/share/vim/vimrc)

3) vscode와 vim 충돌하는 키 disable하기
- `ctrl` + `a`, 전체선택
vim에서는 increment number
- `ctrl` + `v`, 붙여넣기
vim에서는 block select
- `ctrl` + `x`, 잘라내기
vim에서는 decrement number
- `ctrl` + `b`, Vscode의 show sidebar toggle
vim에서는 page up

다음 키를 `ctrl` + `k` + `s` 단축키로 keyboard shortcuts에 접근한 후 해당 단축키들 delete함. 

4) vim 모드 끄고 싶을 때
- `ctrl` + `shift` + `p` 로 shortcut 들어간 후 vim toggle 실행
- vim disabled라고 뜨면 실행 된것

## VIM 단축키 정리



## Reference

- [https://coldmater.tistory.com/191](https://coldmater.tistory.com/191)

- [키보드 단축키 모음](https://gmlwjd9405.github.io/2019/05/14/vim-shortkey.html)
fifbifbifbiebfiebfeifbeifb eoifeifbeifbeibfe