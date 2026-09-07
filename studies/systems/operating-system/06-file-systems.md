---
layout: page
title: "06. 파일 시스템"
permalink: /studies/systems/operating-system/06-file-systems/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Operating_System/lecture_notes/06%20%ED%8C%8C%EC%9D%BC%20%EC%8B%9C%EC%8A%A4%ED%85%9C.md)

{% raw %}
tags: #operating-system #file-system #inode #directory #storage

관련 노트: [운영체제 개요](01-os-overview.md), [IO와 디스크 드라이브](05-io-and-disk-drives.md), [A1 xv6 Shell 과제](a1-xv6-shell.md)

## 핵심 요약

파일 시스템은 저장장치를 file과 directory라는 추상화로 제공한다. File은 byte의 선형 배열이고, directory는 이름을 inode number에 연결하는 mapping 구조다. 운영체제는 `open`, `read`, `write`, `lseek`, `fsync`, `rename`, `link`, `unlink`, `stat` 같은 system call로 storage virtualization을 제공한다.

이 장은 file abstraction, directory tree, inode, file descriptor, random offset I/O, immediate write, metadata, multi-level indexing, directory organization을 다룬다.

## File과 Directory

파일은 이름보다 inode number로 식별되는 byte array다. 이름은 directory entry를 통해 inode에 연결된다. 같은 inode가 여러 이름을 가질 수도 있다.

Directory는 file name과 inode number의 list로 볼 수 있다. Directory hierarchy는 root directory `/`에서 시작하고, path는 directory를 따라 특정 file 또는 directory를 찾는 문자열이다.

## Inode

Inode는 file-system object를 설명하는 자료구조다. 일반적으로 다음 정보를 가진다.

- File type
- Size
- Owner와 permission
- Timestamp
- Data block 위치
- Link count

중요한 점은 inode가 file name 자체를 저장하지 않는다는 것이다. File name은 directory entry에 있고, inode는 해당 object의 metadata와 block mapping을 저장한다.

## File Name과 Path

File name은 보통 `bar.txt`처럼 임의 이름과 extension으로 구성된다. Extension은 관례적으로 파일 형식을 알려주지만, 운영체제가 항상 내용과 extension을 강제적으로 일치시키지는 않는다.

Path는 absolute path와 relative path로 나뉜다.

- Absolute path: `/`에서 시작한다.
- Relative path: 현재 working directory 기준이다.

Shell에서 `.`은 현재 directory, `..`은 parent directory를 의미한다.

## File 생성과 열기

`open()` system call은 file을 열고 file descriptor를 반환한다. `O_CREAT` flag를 주면 파일이 없을 때 새로 만들 수 있다. `O_WRONLY`, `O_RDONLY`, `O_RDWR`은 접근 mode를 지정한다.

File descriptor는 process-local integer handle이다. 같은 inode라도 process마다 다른 descriptor 번호로 열릴 수 있고, descriptor마다 current file offset이 관리된다.

## Read, Write, Offset

`read()`와 `write()`는 file descriptor의 current offset에서 시작해 데이터를 읽거나 쓴다. 호출이 성공하면 offset이 읽거나 쓴 byte 수만큼 증가한다.

`lseek()`은 current offset을 특정 위치로 이동시킨다. 이를 통해 파일 처음부터 순차 접근하지 않고 임의 위치를 읽고 쓸 수 있다.

## Buffered Write와 fsync

`write()`가 반환되었다고 데이터가 즉시 persistent storage에 기록되었다는 뜻은 아니다. 성능을 위해 파일 시스템은 write를 memory buffer에 모아두었다가 나중에 disk에 반영할 수 있다.

`fsync()`는 특정 file의 dirty data와 metadata를 저장장치에 밀어 넣어 crash 이후에도 남을 가능성을 높인다. Database나 editor처럼 durability가 중요한 프로그램은 `fsync()`와 rename pattern을 신중히 사용한다.

## Rename의 의미

`rename()`은 file name을 바꾼다. 많은 editor는 기존 파일을 직접 덮어쓰기보다 임시 파일을 만들고 내용을 쓴 뒤 rename으로 교체한다. Rename은 파일 업데이트를 atomic하게 보이게 만드는 데 유용하다.

다만 진정한 crash consistency를 보장하려면 파일 데이터와 directory metadata의 sync 순서까지 고려해야 한다.

## File Metadata와 stat

`stat` 또는 `fstat()`은 file metadata를 조회한다. `struct stat`에는 inode number, size, type, permission, link count 등의 정보가 들어간다.

Shell의 `ls -l` 같은 명령은 이러한 metadata를 읽어 사람이 보기 좋은 형태로 출력한다.

## Link와 Unlink

Hard link는 같은 inode에 다른 이름을 추가한다. Link count는 inode를 가리키는 directory entry 수를 나타낸다. `unlink()`는 이름 하나를 제거한다. Link count가 0이 되고 열린 file descriptor도 모두 닫히면 파일의 data block을 회수할 수 있다.

이 구조 때문에 "파일 삭제"는 실제로는 이름과 inode 연결을 끊는 동작이다.

## File Organization: Inode 기반 Block Mapping

File system은 file의 logical block이 disk의 어느 physical block에 있는지 알아야 한다. Inode는 direct pointer와 indirect pointer를 사용해 block mapping을 저장한다.

- Direct pointer: 작은 파일을 빠르게 접근한다.
- Single indirect pointer: pointer block 하나를 거쳐 더 많은 data block을 가리킨다.
- Double/triple indirect pointer: 큰 파일을 위해 계층을 늘린다.

Multi-level indexing은 작은 파일에는 낮은 overhead를 유지하고, 큰 파일에는 확장성을 제공한다.

## Directory Organization

Directory는 file name을 inode number로 바꾸는 table이다. 단순 구현에서는 directory file 안에 fixed-size entry를 나열한다. 큰 directory에서는 lookup 성능을 위해 hash나 tree 구조가 쓰일 수 있다.

Path resolution은 `/a/b/c`를 해석할 때 root inode에서 시작해 `a`, `b`, `c`를 차례로 directory lookup하는 과정이다.

## 복습 체크포인트

- File abstraction을 byte array와 inode 관점에서 설명할 수 있는가?
- Directory entry와 inode의 역할 차이를 말할 수 있는가?
- File descriptor와 inode number의 차이를 설명할 수 있는가?
- `lseek()`이 random access를 어떻게 가능하게 하는가?
- `write()`와 `fsync()`의 durability 차이를 말할 수 있는가?
- Hard link와 unlink의 동작을 link count로 설명할 수 있는가?

## 보강 학습 노트

### 큰 그림

- 이 문서는 **06. 파일 시스템**를 다루며, process, scheduling, virtual memory, concurrency, file system을 통해 OS가 hardware resource를 추상화하고 보호하는 방식을 이해한다.
- 단편적인 정의를 외우기보다 입력이 무엇이고, 내부에서 어떤 변환이 일어나며, 출력이나 성능 지표가 어떻게 결정되는지 흐름으로 잡는 것이 좋다.
- 앞뒤 단원과 연결해 보면 이 주제가 왜 필요한지, 어떤 가정을 추가하거나 완화하는지 더 분명해진다.

### 핵심을 더 깊게 보기

- OS의 핵심은 CPU, memory, disk를 각각 process, address space, file이라는 추상화로 바꾸는 것이다.
- concurrency에서는 correctness가 성능보다 먼저이며 race, deadlock, starvation을 구분해야 한다.
- virtual memory는 isolation, relocation, demand paging, caching을 제공하지만 TLB/page fault 비용이 따른다.

### 문제 풀이 또는 구현 루틴

- kernel path를 user call, trap, syscall handler, kernel object update, return 순서로 추적한다.
- 동기화 문제는 shared state, invariant, lock ownership, sleep/wakeup 조건을 먼저 적는다.
- 파일 시스템은 inode, block allocation, directory entry, cache, crash consistency 순서로 본다.
- 마지막에는 단위, 차원, boundary condition, edge case를 확인해 계산 결과가 현실적인지 검산한다.

### 자주 하는 실수

- process와 thread는 address space 공유 여부가 핵심 차이다.
- lock을 잡은 채 sleep하거나 다른 lock 순서를 섞으면 deadlock이 생길 수 있다.
- page table entry bit 하나가 protection, sharing, lazy allocation 동작을 바꾼다.
- 정의를 그대로 적용하기 전에 이 단원에서 전제한 ideal assumption이 실제 문제에서도 유지되는지 확인한다.

### 스스로 점검할 질문

- 이 기능은 어떤 kernel data structure를 바꾸는가?
- 동시 실행될 때 invariant가 깨지는 interleaving은 없는가?
- 성능 병목이 context switch, page fault, lock contention, I/O 중 어디인가?
- **06. 파일 시스템**를 한 문장으로 설명하고, 관련 수식이나 회로/알고리즘/시스템 그림 없이도 핵심 흐름을 말할 수 있는가?

{% endraw %}

---

이전: [05. IO와 디스크 드라이브](05-io-and-disk-drives.md) · 다음: [A1 xv6 Shell 과제](a1-xv6-shell.md)
