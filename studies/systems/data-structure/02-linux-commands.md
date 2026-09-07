---
layout: page
title: "02. Linux 명령어 요약"
permalink: /studies/systems/data-structure/02-linux-commands/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/02%20Linux%20%EB%AA%85%EB%A0%B9%EC%96%B4%20%EC%9A%94%EC%95%BD.md)

{% raw %}
## 경로와 특수 문자

| 표현 | 의미 |
|---|---|
| `/` | 루트 또는 경로 구분자 |
| `.` | 현재 디렉터리 |
| `..` | 상위 디렉터리 |
| `~` | 홈 디렉터리 |
| `*` | 임의의 문자열과 매칭 |
| `?` | 임의의 한 글자와 매칭 |
| `'...'` | 문자열을 그대로 해석 |
| `"..."` | 대부분 그대로 두되 변수 확장은 허용 |
| `\` | 특수 문자 이스케이프 |

절대 경로는 루트부터 시작하고, 상대 경로는 현재 위치를 기준으로 해석된다.

## 디렉터리 명령

| 명령 | 예시 | 설명 |
|---|---|---|
| `pwd` | `pwd` | 현재 위치 출력 |
| `ls` | `ls -al` | 파일 목록 출력 |
| `cd` | `cd src` | 디렉터리 이동 |
| `mkdir` | `mkdir build` | 새 디렉터리 생성 |
| `rmdir` | `rmdir empty` | 빈 디렉터리 삭제 |

## 파일 명령

| 명령 | 예시 | 설명 |
|---|---|---|
| `cat` | `cat main.cpp` | 파일 내용 출력 |
| `cp` | `cp a.cpp b.cpp` | 파일 복사 |
| `mv` | `mv old.cpp new.cpp` | 이동 또는 이름 변경 |
| `rm` | `rm *.o` | 파일 삭제 |
| `diff` | `diff out.txt answer.txt` | 두 파일 비교 |
| `wc` | `wc -l file.txt` | 줄 수, 단어 수 등 출력 |
| `grep` | `grep key *.cpp` | 패턴 검색 |

삭제 명령은 되돌리기 어렵기 때문에 과제 디렉터리에서 `rm -r`를 사용할 때는 경로를 반드시 다시 확인한다.

## 권한

`chmod`는 파일 권한을 변경한다.

| 권한 | 의미 |
|---|---|
| `r` | 읽기 |
| `w` | 쓰기 |
| `x` | 실행 |

실행 파일이 실행되지 않으면 `chmod +x program`이 필요한 경우가 있다.

## 입출력

| 표현 | 의미 |
|---|---|
| `command > out.txt` | 표준 출력을 파일로 저장 |
| `command 2> err.txt` | 표준 에러를 파일로 저장 |
| `command < in.txt` | 파일을 표준 입력으로 사용 |
| `a | b` | `a`의 표준 출력을 `b`의 표준 입력으로 전달 |

입출력 리디렉션은 자동 채점 환경과 거의 같은 방식으로 프로그램을 테스트하게 해준다.

## 프로세스 제어

| 명령 | 설명 |
|---|---|
| `jobs` | 현재 쉘의 작업 목록 |
| `fg` | 백그라운드 작업을 전면으로 |
| `bg` | 중지된 작업을 백그라운드로 |
| `kill` | 프로세스에 신호 전달 |
| `ps` | 프로세스 목록 확인 |
| `exit`, `logout` | 쉘 종료 |

명령 뒤에 `&`를 붙이면 백그라운드로 실행할 수 있다.

## 기타 유용한 명령

| 명령 | 설명 |
|---|---|
| `passwd` | 비밀번호 변경 |
| `date` | 현재 시각 출력 |
| `printenv` | 환경 변수 출력 |
| `echo` | 문자열 출력 |
| `who` | 접속 사용자 확인 |

## vi 최소 생존 명령

| 명령 | 의미 |
|---|---|
| `i` | 입력 모드 |
| `Esc` | 일반 모드 |
| `:w` | 저장 |
| `:q` | 종료 |
| `:wq` | 저장 후 종료 |
| `:q!` | 저장하지 않고 종료 |
| `/word` | 검색 |
| `dd` | 한 줄 삭제 |
| `yy` | 한 줄 복사 |
| `p` | 붙여넣기 |

## 함께 보면 좋은 노트

- [Linux 기본](01-linux-basics.md)
- [C++ 입출력](07-cpp-io.md)


{% endraw %}

---

이전: [01. Linux 기본](01-linux-basics.md) · 다음: [03. C++ 타입](03-cpp-types.md)
