# libasm (어셈블리어로 라이브러리 만들기)

## 어셈블리어란?

어셈블리어는 여러 코딩 언어가 나오기 전 기계어 (숫자)로만 프로그램을 만들 때 보기 편하라고 만든 언어이다.

예를 들어서 아주 간단한 기능을 c언어로 만든다 생각해보자.

```c
int a;
int b;
int c;
a = 1;
b = 2;
c = a + b;
```

a와 b라는 정수형 변수에 각각 1, 2를 할당하고 c라는 변수에 a와 b를 더한 값을 집어넣는 코드다. 만약에 위 코드를 기계어로 작성한다면

```
c7 45 fc 00 00 00 00
c7 45 f8 01 00 00 00
c7 45 f4 02 00 00 00
8b 45 f8
03 45 f4
89 45 f0
```

이런 한눈에 봐서 대체 무슨일을 하는지 모를 코드로 작성된다. (위 c 코드를 밑 기계어로 바꾸는 것을 "컴파일" 이라고 한다.)

위처럼 숫자로 프로그램을 만들면 유지보수가 너무 힘들기 때문에 어셈블리어를 사용한다.

```assembly
mov    DWORD PTR [rbp-0x4],0x0 ; 스택에 4바이트 길이의 0을 저장 (위에서 'c' 변수)
mov    DWORD PTR [rbp-0x8],0x1 ; 스택에 4바이트 길이의 1을 저장 (위에서 'a' 변수)
mov    DWORD PTR [rbp-0xc],0x2 ; 스택에 4바이트 길이의 2을 저장 (위에서 'b' 변수)
mov    eax,DWORD PTR [rbp-0x8] ; eax 라는 cpu 레지스터에 a 변수를 꺼내옴
add    eax,DWORD PTR [rbp-0xc] ; eax 라는 cpu 레지스터에 b 변수를 더함
mov    DWORD PTR [rbp-0x4],eax ; c 변수에 eax 레지스터 저장
```

위 기계어 코드 줄(각각 한 줄이 하나의 명령어를 의미한다.)이랑 어셈블리어 코드 줄이랑 1:1 대응이 되는 것이다.

딱 보기에 되게 의미없이 적어놓은 것 같은 위 기계어에서도 비트 필드당 의미하는 일종의 문법이 있다.

8b 45 f8 -> 이렇게 써있는거도 비트 단위로 분석해보면 opcode (값을 더하거나 빼거나 하는 동작을 의미함. opreation code), 연산을 할 대상 등이 들어 있으며 사람이 이를 일일히 분석하여 코드를 작성하거나 유지보수를 하기 너무 어려우므로 어셈블리를 사용하는 것이다.

## C나 자바 등을 놔두고 복잡하고 어려운 어셈블리어를 사용하는 이유는?

어셈블리어 자체는 현재 점유율 11위로 생각보다 널리 또 많이 사용되는 언어이다.

여러가지 이유가 있겠지만 C나 자바등을 이용해서 컴파일을 하면 컴파일러가 임의로 최적화를 하거나 하기 때문에 의도치 않은 동작을 수행하거나 더 느려질 수 있다.

예를 들어 위 C 코드에서는 변수의 선언과 연산을 따로따로 하였지만 기계어로 번역된 결과를 보면 선언과 연산을 동시에 한 것을 볼 수 있다.

최근엔 cpu 성능이 좋아 성능을 위해서 어셈블리어로 코딩을 할 필요는 없지만 컴파일러가 의도치 않은 최적화 등을 하면 예기치않은 문제가 발생할 수 있기 때문에 CPU에서 구동되는 동작을 명령어 단위로 확인해야 하거나 컴파일러를 거치지 않고 직접 그 동작을 정하고 싶을 때에는 어셈블리어를 사용한다.

예를 들면 보안, 해킹, 드라이버 개발 등 여러 분야가 있을 것이다. (특히 해킹 분야에서 많이 사용되는 것으로 알고 있다.)

최근에는 웹 프론트앤드 분야에서도 웹어셈블리 라는 개념으로 부분적으로 사용된다고 한다. 웹어셈블리를 사용할 때 어셈블리어로 직접 코딩하진 않겠지만 어셈블리 관련 지식이 적당히 있어야 할 것으로 보인다.

하지만 어셈블리어로 함수나 기능 몇개가 아니라 프로그램 전체를 만드는 것은 엄청나게 비효율적인 일이므로 특수한 직종이 아니면 어셈블리어를 빠삭하게 알 필요는 없고 어셈블리어가 어떤 것인지 적당히 알기만 하면 도움이 될 것이다. (libasm도 이런 의도로 출제한 과제로 보임)

## C언어로 컴파일 한 실행파일의 기계어/어셈블리어 보기

```
# 컴파일 (실행파일 자체가 기계어임)
$> gcc main.c
# 기계어를 어셈블리어로 보기 (리눅스)
$> objdump -d -M intel ./a.out
# 기계어를 어셈블리어로 보기 (맥os)
$> objdump -d --x86-asm-syntax=intel a.out
```

## 어셈블리어 아주 기초 문법

### 어셈블리어 기본 문법

어셈블리어는 한줄 한줄 하나의 명령으로 이루어진다. 따라서 명령 한줄한줄은 간단한 편이다.

어셈블리어의 명령은 주로 다음과 같이 이루어져 있다.

[opcode] [operand]

### opcode (명령어)

위에 어셈블리어 예제를 보면 mov, add와 같은 문자가 있다. 이것이 opcode이다.

어셈블리어는 opcode가 명령을 나타내며 무슨 동작을 할 지를 의미한다.

opcode는 기계어에서 명령어를 의미하고 어셈블리어에서 명령어는 mnemonic(니모닉) 이라고 부른다.

### operand (인자)

operand는 오퍼랜드라고 읽으며 해당 명령어 (opcode)가 명령어를 어떤 대상에 수행할지를 나타낸다.

### 예제

예를 몇가지 들어보자.

```assembly
mov a, b
add a, b
```

위에서 mov, add가 무슨 명령을 실행할 지 나타내는 opcode가 되고 뒤에 a, b가 오퍼랜드이다.

오퍼랜드에는 메모리 주소, 스택 주소, 상수, 레지스터 등이 올 수 있다.

## libasm

### 요구사항

- asm 파일 및 Makefile을 이용하여 libasm.a (정적 라이브러리) 를 만들 수 있어야 함.
- asm 파일의 확장자는 .s임.
- 어셈블리어의 컴파일러는 nasm을 사용해야 한다.
- Intel 문법을 사용해야 함.
- 64비트 양식으로 작성해야 하며 "calling convention"을 숙지
- Makefile을 작성해야 하며 가지고 있어야 하는 명령어는 기존과 동일, 보너스도 마찬가지
- 프로젝트에 대한 테스트 프로그램(코드)는 선택사항으로 제출하며 이를 이용해 defense 하면 편함. 테스트 프로그램에서는 궂이 코딩룰을 지킬필요 없음 (근데 테스트를 위한 main 함수는 함께 제출하라고 함. 뭐지?)
- 인라인 ASM을 작성하면 안됨
- syscalls 도중에 에러가 발생하는지 반드시 체크해야 하며 필요시 에러 관리
- errno 변수를 적절하게 관리 (이를 위해 extern ___error 호출 허용)

### 구현해야 하는 함수

- ft_strlen (man 3 strlen)
- ft_stcpy (man 3 strcpy)
- ft_strcmp (man 3 strcmp)
- ft_write (man 2 write)
- ft_read (man 2 read)
- ft_strdup(man 3 strdup, malloc을 call할 수 있습니다.)

## 요구사항 분석하며 과제 진행하기

### nasm 사용해보기

gcc(lldb) 가 아닌 nasm 이라는 컴파일러?를 사용하라 한다. 우선 구글링을 통해 nasm 튜토리얼을 찾아본다.

[https://cs.lmu.edu/~ray/notes/nasmtutorial/](https://cs.lmu.edu/~ray/notes/nasmtutorial/)

추가로 현재 개발환경에서 nasm이 설치되어 있지 않다면 설치한다. (apt-get 혹은 brew를 이용해서 설치한다.)

튜토리얼 사이트에 들어가면 hello world 예제가 나오는데 해당 예제를 실행해보자.

```assembly
; ----------------------------------------------------------------------------------------
; Writes "Hello, World" to the console using only system calls. Runs on 64-bit macOS only.
; To assemble and run:
;
;     nasm -fmacho64 hello.asm && ld hello.o && ./a.out
; ----------------------------------------------------------------------------------------

          global    start

          section   .text
start:    mov       rax, 0x02000004         ; system call for write
          mov       rdi, 1                  ; file handle 1 is stdout
          mov       rsi, message            ; address of string to output
          mov       rdx, 13                 ; number of bytes
          syscall                           ; invoke operating system to do the write
          mov       rax, 0x02000001         ; system call for exit
          xor       rdi, rdi                ; exit code 0
          syscall                           ; invoke operating system to exit

          section   .data
message:  db        "Hello, World", 10      ; note the newline at the end
```

```sh
$> nasm -fmacho64 hello.s && ld -macosx_version_min 10.7.0 hello.o && ./a.out
```

해당 어셈블리 코드로 작성된 파일을 컴파일하면 오브젝트(hello.o)파일이 생성된다.

c 파일을 컴파일 할 때 도중에 나오는 o 파일과 동일하며 이를 실행 파일로 만들기 위해선 링커 (ld) 명령어를 이용해서 링크한 후 실행파일 (a.out) 으로 만들어야 한다.

nasm에서 -fmacho64 옵션은 맥os 상에서 구동되는 64비트 환경으로 컴파일하라는 의미이다.

참고로 위에서 사용했던 objdump 명령어를 이용하면 컴파일 된 오브젝트 파일이나 실행파일을 역으로 디컴파일 할 수 있다.

```shell
objdump -d --x86-asm-syntax=intel hello.o

hello.o:	file format Mach-O 64-bit x86-64


Disassembly of section __TEXT,__text:

0000000000000000 start:
       0: b8 04 00 00 02               	mov	eax, 33554436
       5: bf 01 00 00 00               	mov	edi, 1
       a: 48 be 00 00 00 00 00 00 00 00	movabs	rsi, 0
      14: ba 0d 00 00 00               	mov	edx, 13
      19: 0f 05                        	syscall
      1b: b8 01 00 00 02               	mov	eax, 33554433
      20: 48 31 ff                     	xor	rdi, rdi
      23: 0f 05                        	syscall
```

오퍼렌드 명칭이 약간 다른 것을 제외하면 거의 비슷한 것을 볼 수 있다. 이는 본질적으로 직접 실행되는 기계어와 어셈블리어가 거의 같음을 보여준다.

튜토리얼에 없는 옵션 -macosx_version_min 10.7.0을 붙인 이유는 맥os에서 하이시에라 os 버전부터 main이 없는 코드의 실행을 불가능하게 바꿔 이전 버전으로 컴파일 하는 것이다. 추후에 라이브러리를 만들 때에는 해당 옵션을 붙일 필요 없다.

### nasm에서 사용하는 어셈블리어 기본 문법

어셈블리어는 컴파일러나 CPU에 따라 문법이 조금씩 다르다. 하지만 기본적인 틀은 비슷하다. nasm에서 사용하는 어셈블리어 문법을 보자.

참조 : [https://www.tutorialspoint.com/assembly_programming/assembly_basic_syntax.htm](https://www.tutorialspoint.com/assembly_programming/assembly_basic_syntax.htm)

어셈블리어로 코딩할 때 코드는 크게 3가지 부분으로 나누어진다.

- .data
- .bss
- .text

#### .data

초기값이 존재하는 상수가 들어간다. 실행 도중 여기에 있는 데이터는 변화하지 않는다. 버퍼 사이즈, 상수값 등을 여기에 저장한다.

#### .bss

초기값이 없는 변수가 여기에 들어간다. 상수가 아닌 변수가 여기에 들어간다.

#### .text

실제 어셈블리 코드가 여기에 들어간다. text 섹션 시작 바로 직전에 global [라벨명] 을 집어 넣어 코드의 시작 부분을 명시한다.

#### 주석

nasm 에서 주석은 세미콜론을 이용해 단다.

#### 명령어 한줄한줄

```
[label]   mnemonic   [operands]   [;comment]
```

위 어셈블리어 아주기본에서 보다시피 명령어 핵심 구성요소는 미모닉(opcode)과 오퍼랜드이다. 라벨과 주석은 선택사항이다.

### 메모리

메모리 접근은 대괄호를 이용해서 한다.

필요시에 대괄호 앛에 크기를 명시해야 한다.

### 레지스터

레지스터는 CPU 내부에 있는 저장장치이다. 레지스터에 대한 접근 속도는 메모리에 대한 접근 속도보다 훨씬 빠르므로 꼭 필요한 경우가 아니면 레지스터에 접근을 하여 연산한다.

레지스터는 단순히 값을 잠시 저장하는 것 뿐만이 아니라 여러 가지 다른 기능도 수행한다.

레지스터를 분류하면 다음과 같다. (세부적인 레지스터의 명칭과 기능은 CPU 제조사마다 다르다.)

### 범용 레지스터 (Intel 64bit CPU 기준)

- 데이터 레지스터
  - Accumulator (누산기, 산술 연산에 사용), Base (주소 지정에 사용), Count (반복문 카운트), Data (Data I/O) 4가지 레지스터가 대표적이다.
  - 64비트 CPU에서는 64비트 길이를 가지며 32비트 CPU에서는 32비트 길이를 가진다.
  - 위 레지스터에 대해 일부분만 접근이 가능하다.
    - Accumulator, Base, Count, Data 각각 앞 알파벳을 따서 A, B, C, D로 구분하는 것을 기본으로 앞뒤에 접두사를 붙여 길이를 구분한다.
      - R@X - E@X - @X - @H - @L (@ = A, B, C, D)
        [64비트] - [32비트] - [16비트] - [16비트의 왼쪽 8비트] - [8비트]
      - 예를 들어 64비트의 누산기 레지스터는 RAX이며 16비트 카운트 레지스터는 CX이다.
    - 위 4가지 레지스터는 원래 본 기능이 존재하지만 다른 용도로도 많이 사용한다. (예를 들면 시스템 콜)
- 포인터 레지스터
  - 메모리의 주소를 가리킨다.
    - IP (Instruction Pointer, 명령어 포인터) : 다음에 실행될 명령어가 들어있는 메모리 주소를 가리킨다.
    - SP (Stack Pointer) : 프로그램이 사용하는 스택 메모리의 주소를 가리킨다.
    - BP (Base Pointer) : 스택의 특정 기준점을 의미함.
  - 64비트에서 각 포인터는 RIP, RSP, RBP이다.
  - 보통 서브 루틴의 지역 변수는 스택에 저장되기 때문에 C에서 지역변수를 선언하면 해당 변수는 스택에 쌓이게 된다.
- 인덱스 레지스터
  - 보통 메모리에 일렬로 저장된 데이터에 접근 시 사용함.
  - SI (Source Index)
  - DI (Dest. Index)

### 제어 레지스터

- 플래그 형태로 사용하며 산술연산을 할 때에나 인터럽트 사용 여부 등을 정할 수 있다.
- 산술 연산을 할 때 오버플로우가 발생하거나 연산 결과가 짝수이거나 등을 플래그로 확인할 수 있다.

### 세그먼트 레지스터

- 프로그램에서 메모리의 어떤 부분이 사용되는지 명시한다. 예를 들면 코드 영역 / 데이터 영역 / 스택 영역이 메모리에서 구분되는데 이런 구분을 하기 위해서 사용한다.

### Syscall

- System Call = syscall
- 운영 체제(커널)가 기본적으로 제공하는 서비스가 있음. (화면에 글자 출력하는것부터 파일 열고 닫기, 통신 등등)
- 예를 들어 printf나 write 함수를 사용해서 쉘에 글자를 출력하는 것은 printf나 write 라는 표준 함수를 이용해서 화면에 출력하는 것이지 사용자가 직접 화면에 픽셀을 바꿔서 글자를 출력하는 것이 아님.
- 왜냐 하면 그런 일들은 운영체제(커널)가 할 수 있는 일임. system call은 커널에 그런 것을 하도록 요청하는 것임.
- 어셈블리어로 코딩을 할 때에 화면 입출력, 파일조작 등 시스템 기능에 접근해야 할 일이 있을 때 Syscall을 이용한다.

### NASM에서 시스템콜 사용하기

1. 누산기 레지스터 (RAX)에 시스템 콜 넘버를 집어넣는다.
2. 시스템 콜 매개변수를 Base, Count, Data 등 CPU 레지스터에 집어넣는다.
3. System Call 명령을 실행한다. (참고로 32비트 환경에서는 int 0x80 명령어로 시스템 콜을 호출한다.)
4. System Call 명령이 실행되고 반환된 결과는 보통 누산기 레지스터에 저장된다.

## 서브루틴 (함수) 호출

참고 : [https://refspecs.linuxbase.org/elf/x86_64-abi-0.99.pdf](https://refspecs.linuxbase.org/elf/x86_64-abi-0.99.pdf) 22p

참고2 : [http://6.s081.scripts.mit.edu/sp18/x86-64-architecture-guide.html](http://6.s081.scripts.mit.edu/sp18/x86-64-architecture-guide.html)

함수 콜 명령어는 call (시스템콜일경우 syscall)을 이용해 서브루틴(함수)을 호출한다.

함수에 입력되는 인자 혹은 리턴값이 있을 때 서브루틴에 인수를 넘겨주거나 인수를 받아야 한다.

입력되는 인수 개수가 6개 이하이고 전부 다 정수값을 가진다면 DI-SI-Data-Count-R8D-R7D 순서대로 인수를 집어넣고 함수를 호출한다. 인수가 6개를 넘어갈 때에는 스택에 변수를 집어 넣는다.

인수에 소수 값이 있을 경우 XMM0 ~ X007에 순서대로 삽입한다.

이후 call 명령을 통해 서브루틴으로 건너뛴다.

함수의 리턴값은 누산기 레지스터에 들어간다. (반환하는 자료형 길이에 따라 R@X - E@X - @X - @H - @L 식으로 명칭이 바뀌는것을 기억하자.)

### NASM에서 변수 선언

보통 변수 이름 - 변수 형식 - 변수 (,로 구분되는 변수가 여러개 올 수 있음.)로 선언한다.

예로 들면

```assembly
message:  db        "Hello, World", 10
```

message : 이름

db : 변수 형식

"Hello, World", 10 : "Hello, World\n"

가 된다.

db, dw, dd, dq, dt는 순서대로 바이트, 워드(2바이트), 더블워드(4바이트), 쿼드워드(8바이트), 10바이트 이다.

## 예제 프로그램 뜯어보기

```assembly
          global    start

          section   .text
start:    mov       rax, 0x02000004     ; write() 기능을 하는 시스템 콜을 사용하기 위해
                                        ; 누산기 레지스터에 sys/syscall.h에 명시되어 있는
                                        ; (SYS_write) 값을 집어넣음
                                        ; write(int fildes, const void *buf, size_t nbyte);
          mov       rdi, 1              ; fildes에 정수 1 대입
          mov       rsi, message        ; *buf에 출력할 메모리 주소 대입
          mov       rdx, 13             ; nbyte에 출력할 바이트 길이 대입
          syscall                       ; 시스템 함수 콜
          mov       rax, 0x02000001     ; exit(int status);
          xor       rdi, rdi            ; status에 0 대입 (mov rdi, 0 과 동일함.)
          syscall                       ; 시스템 콜

          section   .data
message:  db        "Hello, World", 10  ; "Hello, World\n"
```

### 동일한 기능을 C언어로 표현

```c
#include <unistd.h>
#include <stdlib.h>
int main(void)
{
  char	message[13] = "Hello, World\n";
  write(1, message, 13);
  exit(0);
  return (0);
}
```

### 라벨

start: , message: 가 라벨이다. 이 값은 컴파일 하고 나면 저 명령어가 들어있는 주소로 변경된다.

### 시스템 콜과 표준 함수와의 관계

위에서 대충 설명했다시피 콘솔창에 입출력하는것과 프로그램을 종료하는 것은 직접 만든 프로그램이 할 수 없으므로 운영체제한테 위탁하는 식으로 처리한다. C로 코딩하면 그러한 행동을 운영체제(커널)한테 위탁해 주는 함수가 write() 함수와 exit() 함수이다.

그런데 위 어셈블리어를 컴파일해서 바이너리로 만든 것과 밑에 적어둔 C로 작성한 코드를 컴파일해서 바이너리로 만든 것을 각각 디스어셈블(디스어셈블이란 역어셈블 즉 기계어를 어셈블리어로 바꾸는것을 말한다. 그래서 사실 어셈블리어를 컴파일한다라는 표현보다 어셈블리어를 어셈블한다는 것이 더 정확한 표현이다.) 해보면 함수를 call 하는 부분이 특히 다른것을 볼수있다.

위 코드를 gcc(clang)로 컴파일 후 디스어셈블 한 결과는 다음과 같다.

```shell
$> gcc -o test main1.c
$> ./test
Hello, World
$> objdump -d --x86-asm-syntax=intel test
```

```assembly
0000000100003f20 _main:
100003f20: 55                         				 	push	rbp
100003f21: 48 89 e5                   				 	mov	rbp, rsp
100003f24: 48 83 ec 20               				  	sub	rsp, 32
100003f28: c7 45 fc 00 00 00 00   				     	mov	dword ptr [rbp - 4], 0
100003f2f: 48 b8 2c 20 57 6f 72 6c 64 0a       	movabs	rax, 748842676800397356
100003f39: 48 89 45 ed                				 	mov	qword ptr [rbp - 19], rax
100003f3d: 48 b8 48 65 6c 6c 6f 2c 20 57       	movabs	rax, 6278066737626506568
100003f47: 48 89 45 e8                				 	mov	qword ptr [rbp - 24], rax
100003f4b: bf 01 00 00 00          				    	mov	edi, 1
100003f50: 48 8d 75 e8             				    	lea	rsi, [rbp - 24]
100003f54: ba 0d 00 00 00         				     	mov	edx, 13
100003f59: e8 12 00 00 00         				     	call	18 <dyld_stub_binder+0x100003f70>
100003f5e: 31 ff                 				      	xor	edi, edi
100003f60: 48 89 45 e0           				      	mov	qword ptr [rbp - 32], rax
100003f64: e8 01 00 00 00        				      	call	1 <dyld_stub_binder+0x100003f6a>
```

100003f28 부터 100003f47 까지는 message 변수를 만들어 그 안에 메세지 주소를 집어 넣는 부분때문에 추가된 것이다.

100003f4b 부터 100003f54 까지 3줄이 함수를 호출하는 데 사전에 레지스터에 인수를 집어 넣는 과정이다.

100003f50에 mov 대신 lea 명령어가 사용되었는데 두 명령어의 역할을 비슷하다. (자세한건 검색하면 나온다.)

가장 다른 점이 syscall 대신 일반 call 명령어가 사용되었다는 건데 시스템 콜을 사용하지 않고 다른 라이브러리 내의 함수를 호출해서 사용하는 것을 볼 수 있다. (일반적으로 C같은 고급언어로 작성된 상태에서는 프로그램이 직접 시스템 콜을 사용하지 못해 그런 것으로 보인다.)

### 시스템 콜 레퍼런스

하지만 우리는 직접 시스템 콜을 이용해서 라이브러리를 만들 것인데 이런 시스템 콜은 어디서 정보를 얻을 수 있을까?

일단 시스템 콜이 정확히 무엇인지를 다시 생각해보자.

### 시스템 콜 (운영체제)

참조 : [https://fjvbn2003.tistory.com/306](https://fjvbn2003.tistory.com/306)

시스템 콜을 이해하려면 먼저 운영체제가 유저 영역과 커널 영역이 분리되어 동작되는 것을 이해해야 한다.

우리가 키보드나 마우스로 실행하는 모든 프로그램은 유저 영역에서 프로그램이 구동된다. 하지만 우리가 컴퓨터를 켜면 화면이 나오고 컴퓨터가 뭔가 내부적으로 잘 동작되는 것을 알 수 있다. 이런 운영체제 내부에서 동작되는 것은 커널 모드에서 동작되는 것이며 이런 것들을 유저 영역에서 함부로 다룰 수 없다. (운영체제가 오동작 하거나 하는 문제 등 여러 문제가 있다.)

자세한 것은 컴퓨터 공학-운영체제에 대해서 별도로 공부해야 할 것이다.

아무튼 커널 영역에서만 동작되어야 하는 파일 열기 닫기나 읽기 쓰기 프로세스 종료 이런것들은 일반 프로그램이 직접 수행할 수 없기 때문에 시스템 콜을 이용해 운영체제 커널 영역에서 동작하는 프로그램에게 해달라고 대리를 부탁하는 것이다.

C나 C++ 에서 시스템 콜 함수는 그냥 write(); exit() 이렇게 사용하면 되는데 어셈블리어 에서는 어떻게 사용하는 것이 문제가 되었다.

### *nix, 어셈블리어 환경에서 시스템 콜 호출

"linux 64bit system calls in assembly" 와 같은 문장을 구글링 해보면 <sys/syscall.h> 내에 명시가 되어 있다고 한다.

[https://filippo.io/linux-syscall-table/](https://filippo.io/linux-syscall-table/) 의 링크에 따르면 C에서 사용하던 시스템 콜과 RAX 레지스터에 입력해야 하는 번호와 매칭이 되는 테이블이 있다.

위 예시를 보면 Mac OS 환경에서는 RAX 레지스터에 어떤 값을 더해야 하는 것으로 보인데 해당 이유는 [https://stackoverflow.com/questions/48845697/macos-64-bit-system-call-table](https://stackoverflow.com/questions/48845697/macos-64-bit-system-call-table) 에 나와있다. Mac OS에서는 시스템 콜 번호를 별도로 구분해놓았으며 매직넘버 0x2000000을 OR 연산 한 값을 RAX 레지스터에 삽입해야 정상적으로 시스템 콜 호출이 된다고 한다.

### 어셈블리 명령어

어셈블리 명령어는 많은 종류가 있다. 또 CPU마다 명령어가 다르고 명칭도 다르기 때문에 모든 명령어를 외우는 것은 비효율적이다. 하지만 범용적으로 사용되는 명령어 몇개가 있다.

명령어 레퍼런스는 인텔에서 (개발환경이 인텔 64비트 CPU이므로 인텔 레퍼런스를 참조해야한다.) [https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-instruction-set-reference-manual-325383.pdf](https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-instruction-set-reference-manual-325383.pdf) 다음과 같이 레퍼런스를 제공한다.

그래서 처음에는 다른 프로그램 예제들을 보고 구현하고자 하능 기능이 있을때 찾아서 사용하는 것이 효율적인것 같다.

나는 먼저 C로 컴파일 한 후 생성된 바이너리를 역어셈블 하여 내가 사용한 연산 혹은 기능에 어떤 명령어가 사용되는지 확인하여 이게 무슨 명령어인지 레퍼런스 혹은 구글링으로 찾아보는 방법을 사용했다.

#### 산술연산

- a에 1을 더함 : inc a
- a에 1을 뺌 : dec a
- a에 b를 더함 : add a, b
- a에 b를 뺌 : sub a, b
- a에 b를 곱함 : mul a, b (mul 외에 여러 변형이 있을 수 있음)
- a에 b를 나눔 : div a, b (div 외에 여러 변형이 있을 수 있음)
- a에 b를 나는 나머지 : 별도 명령어가 없고 나누면 나머지 값이 별도 레지스터에 저장됨

#### 스택 연산

CPU 내부에서는 스택을 사용한다. (스택 메모리 자체가 CPU 내에 있는게 아니라 스택 포인터를 이용해 외부 메모리에 접근하는 식이다.)

- 스택에 값 a 넣기 : push a
- 스택에서 값 a 빼기 : pop a

#### 값 대입

- a에 b값 넣기 : mov a, b

#### 값 비교 후 분기

- cmp 명령어로 비교함 : cmp a, b
- 비교한 결과는 플래그 레지스터에 저장됨
- jXX 명령어로 원하는 부분으로 분기함

#### 서브루틴 호출 및 복귀

- 일반 서브루틴 호출 : call [서브루틴]
- 시스템 콜 호출 : syscall (단 시스템 콜을 사용하기 전에 특정 레지스터에 시스템 콜 번호를 넣어야 함)
- 종료 후 이전 루틴으로 복귀 : ret
- 보통 서브루틴의 리턴값은 누산기 레지스터에 저장한다.

## 어셈블리어로 모듈 만들어보기

앞서 nasm으로 어셈블리 코드를 컴파일하면 .o 파일이 생성된다. .o 파일은 C로 컴파일할 때에도 중간과정에서 생성되는 파일이다.

object 파일은 쉽게 말하면 C 파일을 기계어로 변환한 코드이며 C 파일에 들어있는 함수명(이런건 심볼정보에 해당), 컴파일된 기계어 코드, 데이터 등이 들어있다.

따라서 어셈블리어로 모듈을 만들 때 적당히 global로 현재 기능하는 코드에 심볼을 할당하고 로직을 작성하면 이것이 모듈이 된다.

### 모듈 예제

```assembly
;func.s / char	genascii(void);
global		_genascii

section		.text
_genascii:			MOV		AL, 119			; 누산기 레지스터에 리턴할 값을 집어넣음. (119 -> ascii 'w')
								RET								; return
```

```c
// main.c
#include <stdio.h>
char	genascii(void);
int main(void)
{
	char c;
	c = genascii();
	printf("%c\n", c);
}
```

```shell
$> nasm -fmacho64 func.s
$> gcc main.c func.o
$> ./a.out 
w
```

어셈블리어에서 함수명 심볼에 언더바를 붙이는 이유는 간단하게 설명하면 C에서 어셈블리로 컴파일하거나 어셈블리로 컴파일 된 함수를 호출할 때에는 앞에 언더바를 붙이는 것이 일종의 규칙이다.

어셈블리 명령어나 레지스터명은 대소문자를 가리지 않는다,

### 모듈 예제 2

```assembly
;addascii.s / char	addascii(char);
global		_addascii

section		.text
_addascii:			MOV		AL, DIL		; 입력 인수를 누산기 레지에 더함
								CMP		AL, 'a'		; 누산기에 저장된 입력 인수와 ascii 'a'와 비교
								JL		rtn				; 저 아스키 코드보다 작으면 종료 루틴으로 건너뜀
								CMP		AL, 'z'		; 누산기에 저장된 입력 인수와 ascii 'z'와 비교
								JG		rtn				; 저 아스키 코드보다 크면 종료 루틴으로 건너뜀
								INC		AL				; 아스키 코드 1 증가
								JMP		rtn				; 리턴 루틴으로 건너뜀

rtn:						RET							; 종료
```

```c
// main.c
#include <stdio.h>
char		addascii(char c);
int			main(void)
{
	char	ct;
	for (char c = 'a'; c < 'z'; c++)
	{
		ct = addascii(c);
		printf("%c -> %c\n", c, ct);
	}
	for (char c = 'A'; c < 'Z'; c++)
	{
		ct = addascii(c);
		printf("%c -> %c\n", c, ct);
	}
}
```

```shell
$> nasm -fmacho64 addascii.s
$> gcc main.c addascii.o
$> ./a.out 
[소문자 알파벳은 다음 문자가 더해지고 대문자 알파벳은 그대로 출력됨]
```

소문자 알파벳을 입력받아 다음 인덱스에 있는 아스키 코드를 반환하고 소문자 알파벳 범위에 있지 않을 경우 입력 값 그대로 반환하는 예시이다.

### errno와 errno.h

요구사항을 보면 errno 변수를 상황에 맞게 set해야 한다고 한다. 먼저 errno가 무엇인지 알아야 한다.

errno는 errno.h 헤더파일에 정의된 static int 변수이다. errno.h는 C 표준 라이브러리에 존재한다.

errno의 목적은 프로그램 코드 (특히 시스템 콜 함수)실행 시 여러 에러가 발생했을 때 에러 내용을 errno에 기록하여 무슨 에러가 발생했는지 확인하기 위함이다.

위에서 말하는 모든 함수에 대해 errno를 적용할 필요는 없다. 표준 함수를 재구현 하는 것이기 때문에 표준함수 메뉴얼에 error 항목에 따라 설정하면 된다.

자세한 내용은 man errno 참조

### ___error ?

요구사항에 추가로 __error 함수가 호출될 수 있다고 한다. errno와 error 함수의 관계를 알아야 한다.

구글이 "apple errno.h" 라고 검색하면 errno.h 소스코드를 볼 수 있는데 소스코드 내에 errno의 정의를 확인할 수 있다.

```c
#define errno (*__error())
```

errno의 정체는 __error() 함수의 반환값이 가리키는 메모리 영역이다.

기존 C에서 시스템 콜을 사용할 때 에러 발생 시 자동으로 errno가 set된다고 하니 errno 변수는 __error() 함수의 반환값이 가리키는 포인터 값이 되는 것으로 보인다.

## strlen 구현

strlen을 구현해보자.

strlen을 구현하기 전 먼저 환경 구성부터 해보자.

### 필요 파일

Makefile - nasm으로 *.s 파일이 *.o 파일로 컴파일되도록 해야 하며 ar 명령어를 이용하여 정적 라이브러리로 만들도록 해야 한다.

ft_strlen.s - 소스 코드

libasm.h - 라이브러리를 생성하는 데에는 필요 없지만 해당 라이브러리를 다른 프로젝트에서 사용할 때 필요하다. 예를 들어 main 함수를 만들어 테스트를 해야 하는데 필요하다.

main 함수를 포함한 C 파일 - main 함수를 이용해서 테스트 케이스를 보여줘야 하므로 필요하다.

```makefile
%.o: %.s
	$(ASMCOMPILER) $(ASMFLAGS) $<

$(NAME): $(OBJS)
	ar rc $(NAME) $(OBJS)
	ranlib $(NAME)
```

Makefile의 일부분이다. 이런 식으로 작성하면 된다.

### man strlen

man strlen에 따르면 strlen의 함수의 원형은 다음과 같다. size_t는 unsigned int와 같은 의미이다.

```
size_t strlen(const char *s);
```

별다른 에러 처리는 없고 기존에 구현한 로직대로 구현하면 될 것이다.

