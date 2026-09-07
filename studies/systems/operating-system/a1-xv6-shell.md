---
layout: page
title: "A1 xv6 Shell 과제"
permalink: /studies/systems/operating-system/a1-xv6-shell/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/A1%20xv6%20Shell%20%EA%B3%BC%EC%A0%9C.md)

tags: #operating-system #xv6 #shell #fork #exec #pipe

관련 노트: [CPU 가상화 - 프로세스와 스케줄링](02-cpu-virtualization-processes-and-scheduling.md), [파일 시스템](06-file-systems.md)

## 과제 목표

이 과제는 xv6-riscv에서 간단한 shell인 `ysh`를 구현하는 것이 목표다. Shell은 사용자 명령을 읽고, 필요하면 child process를 만들고, child에서 프로그램을 실행한다. 기본 skeleton에는 `cd`와 `exit` 같은 built-in command만 동작하므로, 일반 command 실행과 redirection, pipe, background execution을 구현해야 한다.

## 핵심 개념

Shell은 운영체제 process API의 종합 예제다.

- `fork()`: 명령 실행을 위한 child process 생성
- `exec()`: child process를 실제 명령 프로그램으로 교체
- `wait()`: foreground command 종료 대기
- `open()`, `close()`, `dup()`: I/O redirection 구현
- `pipe()`: pipeline 구현

`cd`와 `exit`은 child에서 실행하면 parent shell 상태가 바뀌지 않으므로 shell 자체에서 처리하는 built-in으로 남겨야 한다.

## 구현 대상

주요 구현 위치는 `user/ysh.c` 계열의 shell skeleton이다. 입력 command line을 parsing해 command type을 구분하고, `runcmd()` 흐름에서 각 기능을 수행한다.

구현해야 할 기능:

- 일반 foreground command 실행
- argument parsing
- input/output redirection
- pipe command
- background command
- `cd`, `exit` built-in 유지

## 일반 명령 실행

사용자가 `ls /usr/bin` 같은 명령을 입력하면 shell은 child를 만들고 child에서 `exec(argv[0], argv)`를 호출한다. Parent는 foreground command라면 `wait(0)`으로 child 종료를 기다린다.

구현 시 확인할 점:

- `argv`는 null-terminated array여야 한다.
- xv6의 최대 argument 수 제한을 넘으면 error 처리한다.
- `exec()` 실패 시 child에서 error를 출력하고 종료해야 한다.

## Redirection

Redirection은 표준 입력 또는 표준 출력을 파일로 바꾸는 기능이다.

예시:

```text
cat < input.txt
echo hello > output.txt
```

구현 방식:

1. Child process에서 대상 file을 `open()`한다.
2. 기존 stdin 또는 stdout file descriptor를 `close()`한다.
3. `dup()` 또는 descriptor 할당 규칙을 이용해 새 file을 fd 0 또는 fd 1에 연결한다.
4. `exec()`를 호출한다.

Redirection은 parent shell에 영향을 주면 안 되므로 child에서 처리하는 것이 안전하다.

## Pipe

Pipeline은 한 command의 stdout을 다음 command의 stdin으로 연결한다.

예시:

```text
grep xv6 README | wc
```

구현 방식:

- `pipe()`로 read end와 write end를 만든다.
- 왼쪽 child는 stdout을 pipe write end로 바꾼다.
- 오른쪽 child는 stdin을 pipe read end로 바꾼다.
- 각 process는 쓰지 않는 pipe end를 반드시 닫는다.
- Parent도 pipe fd를 닫고 필요한 child들을 wait한다.

Pipe fd를 닫지 않으면 reader가 EOF를 받지 못해 command가 끝나지 않을 수 있다.

## Background Command

명령 끝에 `&`가 있으면 shell은 child를 기다리지 않고 prompt를 다시 보여준다. Background process는 계속 실행된다.

주의할 점:

- Foreground command와 달리 parent가 즉시 다음 입력을 받는다.
- 필요하면 zombie process 회수 방식을 고려해야 한다.
- 무한 출력 프로그램을 background로 실행하면 prompt와 출력이 섞일 수 있다.

## 검증 포인트

- `cd`, `exit`이 기존처럼 동작한다.
- 일반 명령이 argument와 함께 실행된다.
- Redirection 파일이 제대로 생성되고 내용이 맞다.
- Pipeline에서 왼쪽 출력이 오른쪽 입력으로 전달된다.
- Background command 실행 후 shell이 즉시 prompt로 돌아온다.
- 불필요한 debug message를 출력하지 않는다.

## 실수하기 쉬운 부분

- Built-in `cd`를 child에서 실행해 parent directory가 바뀌지 않는 문제
- `argv` 마지막에 null을 넣지 않는 문제
- Pipe의 unused fd를 닫지 않아 process가 hang되는 문제
- Parent shell의 stdin/stdout을 실수로 바꾸는 문제
- Foreground/background에 따라 `wait()` 호출 여부를 구분하지 않는 문제


---

이전: [06. 파일 시스템](06-file-systems.md) · 다음: [A2 xv6 System Call and Process 과제](a2-xv6-syscall-and-process.md)
