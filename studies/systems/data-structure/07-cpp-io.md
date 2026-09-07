---
layout: page
title: "07. C++ 입출력"
permalink: /studies/systems/data-structure/07-cpp-io/
sitemap: false
---

- **원본 노트**: [GitHub](https://github.com/RayKim3103/Undergraduate-Course/blob/main/%5BUndergraduate%5D_Data_Structure/lecture_notes/07%20C%2B%2B%20%EC%9E%85%EC%B6%9C%EB%A0%A5.md)

## 입출력의 목표

프로그램은 키보드, 파일, 네트워크, 디스플레이 같은 외부 세계와 데이터를 주고받아야 한다. C++는 운영체제와 라이브러리가 제공하는 stream abstraction을 통해 이를 처리한다.

## C++ 프로그램의 입출력 통로

| 통로 | C++ 객체/형태 | 설명 |
|---|---|---|
| Standard input | `cin` | 기본적으로 터미널 입력 |
| Standard output | `cout` | 기본적으로 터미널 출력 |
| Standard error | `cerr` | 오류 메시지 출력 |
| Command-line argument | `argc`, `argv` | 실행 시작 전에 전달 |
| File | `ifstream`, `ofstream`, `fstream` | 파일 읽기/쓰기 |

## 표준 출력과 표준 에러

```cpp
cout << "Hello World!\n";
cerr << "This is an error message\n";
```

`cout`과 `cerr`은 기본적으로 같은 터미널에 보이지만, 리디렉션하면 따로 저장할 수 있다. 일반 결과와 오류 메시지를 구분하면 디버깅과 자동 채점이 편해진다.

## 명령행 인자

```cpp
int main(int argc, char** argv) {
    for (int i = 0; i < argc; ++i)
        cout << argv[i] << "\n";
}
```

명령행 인자는 프로그램 시작 전에 전달되는 값이다. 적은 수의 설정값을 넘기는 데 적합하지만, 큰 입력 데이터에는 표준 입력이나 파일이 더 적합하다.

## 표준 입력

```cpp
int i;
cin >> i;
```

`cin >>`은 타입에 맞게 값을 읽는다. 문자열에서는 공백을 만나면 입력이 끊긴다.

```cpp
string first, last;
cin >> first >> last;
```

공백 포함 한 줄이 필요하면 `getline`을 사용한다.

```cpp
string name;
getline(cin, name);
```

## `stringstream`

`stringstream`은 문자열을 stream처럼 다룬다.

```cpp
string line, first, last;
getline(cin, line);
stringstream(line) >> first >> last;
```

한 줄을 읽은 뒤 내부에서 단어, 숫자 등을 파싱할 때 유용하다.

## 리디렉션과 파이프

| 명령 | 의미 |
|---|---|
| `./program > out.txt` | 표준 출력을 파일로 저장 |
| `./program 2> err.txt` | 표준 에러를 파일로 저장 |
| `./program < in.txt` | 파일을 표준 입력으로 사용 |
| `./rand | ./average` | 첫 프로그램의 출력을 두 번째 프로그램 입력으로 전달 |

파이프는 작은 프로그램을 조합해 큰 작업을 만드는 Unix식 사고방식이다.

## 파일 스트림

| 클래스 | 역할 |
|---|---|
| `ofstream` | 파일 출력 |
| `ifstream` | 파일 입력 |
| `fstream` | 파일 입출력 |

```cpp
ofstream myfile("example.txt");
if (myfile.is_open()) {
    myfile << "This is a line.\n";
    myfile.close();
}
```

파일을 열고, 작업하고, 닫는 흐름을 유지한다.

## 파일 열기 모드

| 모드 | 의미 |
|---|---|
| `ios::in` | 입력 |
| `ios::out` | 출력 |
| `ios::binary` | binary mode |
| `ios::ate` | 처음 위치를 파일 끝으로 |
| `ios::app` | 항상 파일 끝에 추가 |
| `ios::trunc` | 기존 내용을 지우고 새로 씀 |

## 텍스트 파일과 바이너리 파일

텍스트 파일은 줄 단위 처리와 사람이 읽기 쉬운 데이터에 적합하다.

```cpp
string line;
ifstream myfile("example.txt");
while (getline(myfile, line)) {
    cout << line << '\n';
}
```

바이너리 파일은 형식화되지 않은 byte 데이터를 읽고 쓴다.

```cpp
file.read(memblock, size);
file.write(memblock, size);
```

## 파일 위치 제어

| 함수 | 의미 |
|---|---|
| `seekg` | 읽기 위치 이동 |
| `seekp` | 쓰기 위치 이동 |
| `tellg` | 현재 읽기 위치 |
| `tellp` | 현재 쓰기 위치 |

`ios::beg`, `ios::cur`, `ios::end`를 기준으로 offset을 지정할 수 있다.

## 함께 보면 좋은 노트

- [Linux 기본](01-linux-basics.md)
- [C++ 흐름 제어](04-cpp-control-flow.md)



---

이전: [06. C++ 모듈화](06-cpp-modularization.md) · 다음: [08. C++ 객체지향 프로그래밍](08-cpp-oop.md)
