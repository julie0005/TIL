# 인터럽트

CPU utilization을 높이기 위해서 i/o 작업이 일어나거나 예외 상황이 발생했을 때 우선적으로 일을 처리하고 원래 하던 작업으로 돌아오는 것을 말한다.

## 하드웨어 인터럽트

하드웨어가 발생시키는 인터럽트, CPU가 아닌 다른 하드웨어 장치가 CPU에게 요청

- Maskable Interrupt
    - Interrupt Mask가 가능하다. (인터럽트가 발생했을 때 거절할지 수행할지 결정 가능)
- Non Maskable Interrupt
    - 거부 불가(Interrupt Mask 불가능)
    - 정전, 하드웨어 고장 등 어쩔 수 없는 오류

종류

- I/O Interrupt - 입출력 작업 종료 및 입출력 오류
- Power fail Interrupt - 전원 공급 이상
- Machine Check Interrupt - CPU 기능적인 오류
- External Interrupt - I/O 장치가 아닌 타이머 등 의도적으로 프로그램이 중단된 경우 (Timer Interrupt 등 해당)

## 소프트웨어 인터럽트

소프트웨어가 발생시키는 인터럽트로, 사용자 프로그램 등이 스스로 인터럽트 라인을 세팅한다.

소프트웨어 인터럽트 == 내부인터럽트인지는 잘 모르겠음.

종류

- Program Check Interrupt - 0으로 나누기, 불법적인 연산자, Overflow/Underflow 인터럽트
- SVC(Supervisor Call) Interrupt : user program이 하드웨어에 조작을 가하고 싶을 때 운영체제를 관리하는 프로그램인 supervisor를 부르는 것. - system call
    - 포괄적 개념으로 인터럽트라고 하는 것.
    
   <img width="703" alt="image" src="https://user-images.githubusercontent.com/52846807/190074608-f51b0ff7-e83c-4416-b87e-d7a7e2d116d4.png">
    

## 인터럽트 우선 순위

1. 전원 공급 이상
2. CPU의 기계적인 오류
3. 외부 신호 인터럽트
4. I/O
5. 명령어 오류
6. 프로그램 검사
7. SVC

### 판별 기법

### Polling

- 인터럽트 발생 시, 인터럽트 순회하며 인터럽트 요청 플래그를 검사하고 우선순위가 가장 높은 인터럽트를 찾아내 루틴을 수행함
- 많은 인터럽트가 있을 경우, 모두 조사해야 하므로 처리 속도가 느리다.
- 별도의 하드웨어가 필요 없다.

### Vector Interrupt

CPU와 인터럽트 요청 장치 사이에 장치 번호에 해당하는 버스를 직렬이나 병렬로 연결 후, 요청 장치의 번호를 CPU에게 알리는 방식

- 장치 판별을 위한 별도의 프로그램 루틴이 없어 응답 속도가 빠름
- 추가적인 하드웨어 필요
1. Daisy Chain (직렬 우선순위 부여 방식)
    - 인터럽트가 발생하는 모든 장치를 한 개의 회선에 직렬로 연결
    - 장치를 우선순위 높은 순으로 연결
2. 병렬 우선순위 부여 방식
    - 인터럽트가 발생하는 각 장치를 개별적인 회선으로 연결
    - Mask Register의 비트 위치에 따라 우선순위 결정
    - 인터럽트가 처리되고 있는데 우선순위가 높은 인터럽트 발생 시 선점됨.

## 인터럽트 과정

process A 실행 중 디스크에서 데이터 읽어오기

- process A는 `system call` 을 통해 인터럽트를 발생시킴
- CPU는 지금까지의 상태를 PCB(Process Control Block)에 저장함.
    - 수행 중이던 메모리 주소
    - 레지스터 값
    - 하드웨어 상태 등
- PC(Program Counter)에 다음에 실행할 명령의 주소를 저장.
- 인터럽트 벡터를 읽고 ISR(Interrupt Service Routine)로 접프하여 루틴 실행
    - **인터럽트 벡터**: 인터럽트 발생 시 처리해야 할 인터럽트 핸들러의 주소를 인터럽트 별로 보관하고 있는 테이블
        - 인텔x86에서는 이를 IDT(Interrupt Descriptor Table)이라고도 한다.
    - **인터럽트 핸들러**: 실제 인터럽트를 처리하기 위한 루틴.(Interrupt Service Routine)으로도 불림.
        - 인터럽트가 접수되면 각각의 인터럽트에 대응하여 특정 기능을 처리하는 기계어 코드 루틴임.
- ISR 끝에 IRET 명령어에 의해 인터럽트 해제
    - PC, 레지스터 등 복원 후 이전 실행 위치로 돌아옴.

<img width="643" alt="image" src="https://user-images.githubusercontent.com/52846807/190074801-63a66963-ce37-4a19-9c91-5b8bc5d6962f.png">

## 인터럽트와 명령

### 명령어의 종류

**`일반 명령`:** 모든 프로그램이 수행할 수 있는 명령

**`특권 명령`**: 보안이 필요한 명령, 입출력 장치, 타이머 등 하드웨어 접근하는 명령. 특권 명령은 운영체제만 가능하다.

### kernel mode vs user mode

`**kernel mode**`: 운영체제가 CPU의 제어권을 가지고 명령을 수행하는 모드로 일반 명령과 특권 명령 모두 수행할 수 있음.

`**user mode**`: 일반 사용자 프로그램이 CPU 제어권 가지고 명령을 수행하는 모드로, 일반 명령만 수행할 수 있음.

<img width="717" alt="image" src="https://user-images.githubusercontent.com/52846807/190074949-cbb10809-77ed-45e4-8c55-331c0b1924ad.png">
