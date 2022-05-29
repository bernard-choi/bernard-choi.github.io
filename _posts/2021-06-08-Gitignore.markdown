---  
layout: post
title: "[Git] Gitignore파일을 이용하여 파일과 디렉토리를 ignore 처리"
subtitle: "[Git] Gitignore파일을 이용하여 파일과 디렉토리를 ignore 처리"  
categories: Development
tags: Git
comments: true  
---  

## Gitignore 파일이란?

레포 생성 중 DB 계정 정보가 담긴 파일을 빼고 commit하는 방법을 찾아보다가 gitignore의 기능을 알게 되었습니다. Git은 특정 파일이나 디렉토리를 ignore 하도록 설정할 수 있습니다. Git 으로 하여금 해당 파일이나 디렉토리를 관리하는 목록에서 제외하도록 한다는 것입니다. 그중의 한 방법이 바로 사용자의 저장소 내에 `.gitignore` 파일을 만들어 놓는 것 입니다.

`.gitignore` 파일 내에는 다음과 같은 항목들을 가리키는 이름이나 경로가 포함될 수 있습니다.

```
1. 임시 리소스들 예: 캐시 파일, 로그 파일, 컴파일된 코드 등
2. 다른 개발자들과 공유할 필요 없는(혹은 공유되어서는 안되는) 로컬 설정 파일들
3. 로그인 암호나 키 혹은 credential 파일들과 같이 민감한 정보를 담고 있는 파일들
```

## Gitignore 적용 시 효과

- Git 저장소의 최상위 디렉토리에 해당 파일이 존재할 경우, 파일 내에 기술된 규칙은 전체 저장소 내의 모든 파일들과 서브디렉토리에 규칙이 적용됩니다.

- Git에 의해 변경 내역이 추적(track)되지 않습니다.
- `git status` 나  `git diff`와 같은 명령어 수행시에 검출되지 않습니다.
- `git add -A`와 같은 명령어 수행시에도 stage 영역에 추가되지 않습니다.

## 예제

아래에 .gitignore 파일에서 사용되는 glob파일 패턴 기반의 일반적인 규칙들의 사용 예제가 나와있습니다.

```
# # 로 시작하는 줄은 주석에 해당한다.

# 'file.ext' 이라는 이름의 파일을 ignore 처리한다
file.ext

# ignore 규칙을 정의하는 줄에 주석을 함께 섞어 쓰는 것은 허용되지 않는다
# 아래 줄은 'file.ext # not a comment' 라는 이름의 파일을 ignore 처리할 것이다
file.ext # not a comment

# 전체 경로를 통해 파일 ignore 처리하기
# 파일명만 기술된 규칙은 최상위 디렉토리뿐만 아니라 모든 서브디렉토리에 동일하게 적용된다
# 예) otherfile.ext 파일은 하부의 디렉토리 어디에 있던 모두 ignore 처리될 것이다
dir/otherdir/file.ext
otherfile.ext

# 디렉토리 전체를 ignore 처리하기
# 디렉토리 자체와 디렉토리 내의 모든 내용물들이 ignore 처리된다
bin/
gen/

# Glob 패턴 형식을 사용하여 특정 문자를 포함하는 경로를 ignore 처리할 수 있다
# 예를 들어, 아래의 규칙은 build/ 와 Build/ 두가지 경로 모두에 해당한다
[bB]uild/

# / 로 끝나지 않는 경로의 경우에는, 해당 규칙이 기술된 이름을 갖는 파일과 디렉토리 모두에 적용된다
# 따라서, 아래 예제는 `gen` 이라는 이름을 가진 파일들 뿐만 아니라
# 동일한 이름의(`gen`) 디렉토리 및 해당 디렉토리의 내용물까지 모두 ignore 처리하게 된다
bin
gen

# 파일을 확장자 별로 ignore 처리하기
# 아래 기술된 확장자를 갖는 현재 디렉토리와
# 모든 서브디렉토리 내의 파일들이 ignore 처리될 것이다
*.apk
*.class

# 특정 디렉토리 지정과 특정 확장자 지정 규칙을 혼합하여 사용하는 것도 가능하다
# 아래에 기술된 규칙이 ignore 처리할 파일들은
# 위에서 지정한 규칙들이 ignore 처리하는 파일들과 중복된다
java/*.apk
gen/*.class

# 최상위 디렉토리에 존재하는 파일을 ignore 처리하되,
# 서브디렉토리 내의 동일한 이름을 갖는 파일들은 제외하고 싶다면 `/` 를 앞에 붙인다
/*.apk
/*.class

# DirectoryA 라는 이름의 디렉토리가 저장소 내 어떤 위치에 존재하던
# 모두 ignore 처리하고 싶다면 ** 를 앞에 붙인다
# / 를 마지막에 붙이는 것을 잊지 말아야 한다
# 그렇지 않으면 DirectoryA 라는 이름의 디렉토리 뿐만 아니라 파일들까지 ignore 처리하게 된다
**/DirectoryA/
# 이 규칙은 다음 경로들을 ignore 처리할 것이다:
# DirectoryA/
# DirectoryB/DirectoryA/
# DirectoryC/DirectoryB/DirectoryA/
# 이 규칙은 DirectoryA 라는 이름의 파일을 ignore 처리하지는 않는다 (해당 파일이 어느 위치에 존재하든 무관)

# DirectoryA 라는 이름의 디렉토리 하부에 존재하는 DirectoryB 디렉토리를 ignore 처리하되
# 두 디렉토리 사이에 몇 단계의 다른 경로가 포함되어도 상관없이 ignore 하고 싶다면,
# 두 디렉토리 경로 사이에 ** 문자열을 포함시켜 규칙을 작성할 수 있다
DirectoryA/**/DirectoryB/
# 이 규칙은 다음 경로들을 ignore 처리할 것이다:
# DirectoryA/DirectoryB/
# DirectoryA/DirectoryQ/DirectoryB/
# DirectoryA/DirectoryQ/DirectoryW/DirectoryB/

# 위에서 이미 살펴 보았듯이, 특정 파일들을 한꺼번에 ignore 처리하리 위해서 규칙 명세에 와일드카드를 이용할 수 있다
# 이 때, '*' 하나를 단독으로 명시할 경우, .gitignore 까지 포함하여 폴더 내의 모든 파일들이 (의도치 않게) ignore 처리되게 된다
# 이렇듯 와일드카드를 사용하되 특정 파일은 ignore 처리하지 않으려면, ! 를 이용하여 예외 처리를 할 수 있다
# 이렇게 예외처리로 명시된 파일은 ignore 목록에서 제외될 것이다:
!.gitignore

# 파일 이름 중에 해시(#) 문자가 존재할 경우, 백슬래시를 escape 문자로 이용하여 표기할 수 있다
# (1.6.2.1 버전 이후부터 지원)
\#*#
```

## 추적되고 있는 파일을 ignore처리
이미 현재 Git에 의해 관리 되고 있는 파일을 ignore 처리하는 상황이 있습니다. 이미 commit한 DB 계정 정보의 경우 `.gitignore`파일에 추가하여도 계속해서 track되는 문제가 발생했습니다. 이 경우 해당 파일을 index에서 제거해줍니다.

```
git rm --cached <file>
```
위 명령어를 통해, 해당 파일을 수정하더라고 수정 내역은 추적되지 않을 것입니다. 더불어 기존에 해당 파일에 작업했던 내용 역시 여전히 Git 히스토리 상에서 확인할 수 있습니다.
## Reference

- [https://nochoco-lee.tistory.com/46?category=343045](https://nochoco-lee.tistory.com/46?category=343045)
- git notes for professionals
- [https://stackoverflow.com/questions/45400361/why-is-gitignore-not-ignoring-my-files](https://stackoverflow.com/questions/45400361/why-is-gitignore-not-ignoring-my-files)
