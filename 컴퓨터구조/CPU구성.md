# CPU(코어)의 구성 요소

<img width=500 src="https://github.com/user-attachments/assets/e7ce13ea-1a4c-4735-b8fd-755fc229c210">

다중 코어의 CPU 내부는 각 코어가 있고, 코어마다 ALU(arithmetic and logical unit), 제어장치, 여러개의 레지스터가 있으며, CPU에 따라 코어간 데이터를 공유하기 위한 캐시 메모리가 존재한다.

## ALU(arithmetic and logical unit)

ALU는 CPU에서 연산을 처리하는 요소이다. 연산의 경우 덧셈, 뺄셈, 곱셈, 나눗셈과 같은 산술 연산 뿐아니라, AND, OR 등의 논리 연산과 비교 연산 등 다양한 연산을 처리한다. 

<img width=500 src="https://github.com/user-attachments/assets/6f96d7e9-d50c-4e47-8a8c-a5a50e0e5bb8">

> 출처: https://huimang2.github.io/etc/computer-architecture

ALU는 레지스터로 부터 연산할 피연산자를 받아드리고, 제어장치로 부터 어떤 연산을 할 것 인지 제어 신호를 받아드려, 연산을 수행하고, 결과값을 일시적으로 레지스터에 저장한다.

결과값에 따라 추가적인 정보가 필요하다면, 해당 정보를 플래그를 통해 나타낸다. 예를들어 연산의 결과값이 레지스터에 담기에 너무 크다면, 오버플로우가 발생하고, 이를 나타내기 위해 오버플로우 플래그를 1로 변경한다.

이러한 플래그는 플래그 레지스터를 통해 저장된다.
(부호, 제로, 캐리, 오버플로우, 인터럽트, 슈퍼바이저 플래그 등 다양한 플래그가 존재)

> [!NOTE]
> 📌 **ALU 내부 회로들**
> 
> <img width=200 src="https://github.com/user-attachments/assets/ca9906f8-66b0-41a6-be8c-42be204257b2">
> 

## 제어장치(control unit, CU)

제어 장치는 제어 신호를 보내고, 명령어를 해석하는 요소이다.

![3](https://github.com/user-attachments/assets/df7ee848-84ca-4fc1-a842-cdbb68c75306)

> 출처: https://huimang2.github.io/etc/computer-architecture

레지스터로 부터 명령어와 플래그를 전달받아 이를 해석한 뒤 제어 신호를 발생시킨다.

제어 신호의 목적지가 CPU 내부(ALU, 레지스터)일 경우 바로 전달하고, CPU 외부(메모리, 입출력장치)일 경우 제어 버스를 통해 신호를 내보낸다.

## 레지스터

CPU에는 여러 레지스터가 있고, 각 다른 역할을 수행한다.

CPU 마다 레지스터의 수, 이름, 종류가 매우 다양하지만, 대표적인 레지스터들은 다음과 같다.

- 프로그램 카운터(PC)
    - 메모리에서 가져올 명령어의 주소 때문에 Instruction Pointer(IP)라고도 부름
    - 명령어를 처리하면 다음 명령어를 처리하기 위해 +1 씩 카운터를 올린다.
- 명령어 레지스터(IR)
    - 해석할 명령어(PC를 통해 메모리에서 읽어온)가 저장되는 레지스터d
- 메모리 주소 레지스터(MAR)
    - CPU가 읽고자하는 메모리 주소가 저장되는 레지스터
- 메모리 버퍼 레지스터(MBR)
    - 메모리를 통해 읽어온 값 혹은 메모리에 쓸 값(데이터)이 저장되는 레지스터
- 플래그 레지스터(Flag Register)
    - 연산 결과에 따른 추가적인 정보가 필요할 경우 해당 레지스터에 저장
- 범용 레지스터(General Purpose Register)
    - 이름 그대로 범용적으로 사용되는 레지스터
    - 메모리 주소를 저장할 수도 있고, 임시적으로 데이터를 저장할 수도 있다.
- 스택 포인터(Stack Pointer)
    - 메모리 영역 중 스택 영역를 사용할 때 읽어야할 위치를 기록하고 있는 레지스터
- 베이스 레지스터(Base Register)
    - 메모리 주소를 계산할 때 기준이 되는 주소를 저장하는 레지스터
    - 배열 등에 접근할 때 베이스 레지스터에는 시작 주소가 저장되고, 여기에 offset을 더해서 원하는 메모리 주소를 계산

> [!NOTE]
> 📌 **레지스터를 이용한 주소 지정 방식**
> 
> - 명령어의 오퍼랜드(offset)와 프로그램 카운터를 통해 주소를 얻는 Relative Addression Mode
>     - Call 명령어에 주로 사용되는 방식
> - 명령어의 오퍼랜드(offset)와 베이스 레지스터를 통해 주소를 얻는 Base Register Addression Mode
>    - 배열이나 구조체 같은 데이터 블록, 혹은 동적 메모리 영역에 접근할 때 사용
