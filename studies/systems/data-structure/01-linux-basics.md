---
layout: page
title: "01. Linux 기본"
permalink: /studies/systems/data-structure/01-linux-basics/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/01%20Linux%20%EA%B8%B0%EB%B3%B8.md)

{% raw %}
## 핵심 질문

Linux는 자료구조 구현을 실험하고 채점 가능한 형태로 만드는 실행 환경이다. 이 노트의 중심 질문은 “터미널에서 파일을 만들고, 컴파일하고, 실행하고, 결과를 확인하는 흐름을 어떻게 안정적으로 반복할 것인가?”이다.

## Linux와 쉘

Linux는 명령어 기반 작업에 강한 운영체제이고, 쉘은 사용자가 입력한 명령을 해석해 운영체제에 전달하는 프로그램이다. 기본 쉘로는 Bash 계열을 주로 사용한다.

터미널 작업의 장점은 다음과 같다.

- 컴파일, 테스트, 채점 스크립트 실행을 반복하기 쉽다.
- 입출력 리디렉션과 파이프로 프로그램을 조합할 수 있다.
- 원격 서버에 SSH로 접속해 동일한 환경에서 실행할 수 있다.
- Makefile을 이용해 여러 소스 파일을 자동 빌드할 수 있다.

## 도움말과 명령 찾기

| 명령 | 의미 |
|---|---|
| `man command` | 명령어 매뉴얼 확인 |
| `which command` | 실행 파일 경로 확인 |
| `whereis command` | 실행 파일, 소스, 매뉴얼 위치 확인 |
| `locate name` | 파일 데이터베이스에서 이름 검색 |
| `find path condition` | 실제 디렉터리를 순회하며 검색 |

`man`은 섹션별 문서가 있으므로 같은 이름이라도 라이브러리 함수인지 쉘 명령인지 확인해야 한다.

## 파일 시스템 명령

| 명령 | 용도 |
|---|---|
| `pwd` | 현재 디렉터리 출력 |
| `ls` | 파일 목록 확인 |
| `cd` | 디렉터리 이동 |
| `mkdir` | 디렉터리 생성 |
| `cat` | 파일 내용 출력 또는 연결 |
| `cp` | 파일 복사 |
| `mv` | 파일 이동 또는 이름 변경 |
| `rm` | 파일 삭제 |
| `chmod` | 권한 변경 |
| `du` | 디스크 사용량 확인 |

상대 경로와 절대 경로를 구분하는 습관이 중요하다. `.`은 현재 디렉터리, `..`은 상위 디렉터리, `~`는 홈 디렉터리를 의미한다.

## 압축과 아카이브

`tar`는 여러 파일을 하나로 묶고, `gzip`은 파일을 압축한다. 과제 제출 파일을 만들 때 흔히 사용한다.

```bash
tar cvf submit.tar src include Makefile
gzip submit.tar
tar xvf submit.tar
```

## 리디렉션과 파이프

| 기호 | 의미 |
|---|---|
| `>` | 표준 출력을 파일로 저장 |
| `2>` | 표준 에러를 파일로 저장 |
| `<` | 파일을 표준 입력으로 사용 |
| `|` | 앞 프로그램의 출력을 뒤 프로그램의 입력으로 연결 |

자료구조 과제에서는 대량 입력을 직접 타이핑하지 않고 `./program < input.txt`처럼 테스트하는 일이 많다.

## 링크

Linux의 링크는 파일 이름과 실제 파일 데이터를 연결하는 방식이다.

- 하드 링크: 같은 파일 내용을 여러 이름으로 가리킨다.
- 심볼릭 링크: 다른 경로를 가리키는 바로가기 역할을 한다.

## vi 편집기

`vi`는 모드 기반 편집기다.

| 모드 | 설명 |
|---|---|
| Normal mode | 이동, 삭제, 복사, 붙여넣기 |
| Insert mode | 실제 텍스트 입력 |
| Command mode | 저장, 종료, 검색, 치환 |

자주 쓰는 명령은 `i`, `Esc`, `:w`, `:q`, `:wq`, `dd`, `yy`, `p`, `/pattern`이다.

## 컴파일 과정

C++ 프로그램은 보통 다음 단계를 거친다.

1. 전처리: `#include`, `#define` 등을 처리한다.
2. 컴파일: C++ 코드를 어셈블리 수준으로 변환한다.
3. 어셈블: 목적 파일을 만든다.
4. 링크: 여러 목적 파일과 라이브러리를 묶어 실행 파일을 만든다.

여러 파일로 나눈 프로그램은 선언과 정의를 분리해야 한다. 헤더 파일은 인터페이스를 제공하고, `.cpp` 파일은 구현을 담는다.

## Makefile

Makefile은 “어떤 파일이 어떤 파일에 의존하고, 어떤 명령으로 생성되는지”를 적는 빌드 규칙이다.

```makefile
CXX = g++
CXXFLAGS = -std=c++14 -Wall

all: main

main: main.o list.o
	$(CXX) $(CXXFLAGS) -o $@ $^

%.o: %.cpp
	$(CXX) $(CXXFLAGS) -c $<

clean:
	rm -f *.o main
```

| 기호 | 의미 |
|---|---|
| `$@` | 현재 target 이름 |
| `$<` | 첫 번째 prerequisite |
| `$^` | 모든 prerequisite |
| `$?` | target보다 새 파일인 prerequisite |

## 정리

Linux 기본기는 자료구조 자체만큼 중요하다. C++ 코드를 작성하고, 여러 파일을 컴파일하고, 입력을 리디렉션하고, 실행 결과를 비교하는 루틴이 잡혀야 이후 알고리즘 실험이 빨라진다.

## 함께 보면 좋은 노트

- [Linux 명령어 요약](02-linux-commands.md)
- [C++ 모듈화](06-cpp-modularization.md)
- [C++ 입출력](07-cpp-io.md)


{% endraw %}

---

이전: [00. 강의 개요](00-course-overview.md) · 다음: [02. Linux 명령어 요약](02-linux-commands.md)
