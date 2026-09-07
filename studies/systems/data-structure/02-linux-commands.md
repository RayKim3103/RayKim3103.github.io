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

## 보강 학습 노트

### 큰 그림

- 이 문서는 **02. Linux 명령어 요약**를 다루며, C++ 기초와 자료구조/알고리즘을 연결해 데이터를 저장하고 탐색하고 갱신하는 비용을 체계적으로 분석한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- 자료구조 선택은 기능이 아니라 operation별 시간/공간 복잡도와 access pattern의 선택이다.
- C++에서는 객체 수명, copy/move, pointer/reference, const correctness가 자료구조 안정성을 좌우한다.
- 알고리즘 분석은 Big-O뿐 아니라 input size, worst/average case, hidden constant, memory locality를 함께 본다.

### 문제 풀이 또는 구현 루틴

- 필요한 operation을 insert/delete/search/traverse/update로 나눈 뒤 빈도를 추정한다.
- 구현 전 invariant를 적고, 각 함수가 invariant를 보존하는지 확인한다.
- 복잡도는 loop 중첩, recursion recurrence, data movement 비용을 분리해 계산한다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- 포인터 소유권을 명확히 하지 않으면 leak, dangling pointer, double delete가 생긴다.
- 평균 O(1)인 hash table도 충돌과 rehash 비용을 고려해야 한다.
- 정렬/그래프 알고리즘은 안정성, 메모리 사용, 입력 조건에 따라 적합성이 달라진다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 자료구조가 유지해야 하는 invariant는 무엇인가?
- 가장 자주 호출되는 operation의 복잡도는 무엇인가?
- 구현이 edge case인 empty, one element, duplicate, overflow를 처리하는가?
- **02. Linux 명령어 요약**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [01. Linux 기본](01-linux-basics.md) · 다음: [03. C++ 타입](03-cpp-types.md)
