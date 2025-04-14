# 명령어의 구조

<img width=50% src="https://github.com/user-attachments/assets/af21c3ce-59cf-47c1-90f1-84662ffbbe12">

명령어는 Operation Code와 0개 이상(일반적으로 0~3개)의 Operand로 구성되어 있다. Operand의 수에 따라 N-주소 명령어라고 부른다.

- Operation Code
    - 명령어를 통해 수행할 연산 즉, 연산 코드가 담겨있는 영역
    - 대표적으로 데이터 전송, 산술/논리 연산, 제어 흐름 변경, 입출력 제어 등의 연산 코드가 존재한다.
- Operand
    - 연산에 사용할 데이터가 저장되는 위치
    - 데이터 또는 데이터가 위치한 레지스터 혹은 메모리 주소가 담겨있다. 때문에 `Address Field`라고도 부름.

> [!NOTE]
> 📌 **연산 종류**
>
> - 데이터 전송
>     - MOVIL: 데이터를 옮겨라
>     - STORR: 매모리에 저장하라
>     - LOAD (PETCH): 매모리에서 CPU로 데이터를 가져와라
>     - PUSH: 스택에 데이터를 저장하라
>     - POP: 스태의 최상단 데이터를 가져와라 스택에 대한 개념은 100쪽 <좀 더 알아보기)에서 설명합니다.
> - 산술/논리 연산
>     - ADD/ SUISTRACT / MULTIPLY / DIVIDE: 덧셈 / 뺄셈 / 곱셈 / 나눗셈을 수행하라
>     - INCREMENT / DECREMENT: 오퍼랜드에 1을 더하라 / 오퍼랜드에 1을 빼라
>     - AND/OR / NOT: AND /OR / NOT 연산을 수행하라
>     - COMPARE: 두 개의 숫자 또는 TRUE / FALSE 값을 비교하라
> - 제어 흐름 변경
>     - JUMP: 특정 주소로 실행 순서를 옮겨라
>     - CONDITIONAL JUMP: 조건에 부합할 때 특정 주소로 실행 순서를 옮겨라
>     - HALT: 프로그램의 실행을 멈춰라
>     - CALL : 되돌아올 주소를 저장한 채 특정 주소로 실행 순서를 옮겨라
>     - RETURN: CALL을 호출할 때 저장했던 주소로 돌아가라
> - 입출력 제어
>     - READ (INPUT): 특정 입출력 장치로부터 데이터를 읽어라
>     - WRITE(OUTPUT): 특정 입출력 장치로 데이터를 써라
>     - START IO: 입출력 장치를 시작하라
>     - TEST IO: 입출력 장치의 상태를 확인하라

# 주소 지정 방식(Addressing Mode)

Operand에 연산에 사용할 데이터가 직접적으로 담기는 경우도 있지만, 명령어를 통해 표현할 수 있는 데이터의 한계가 있기 때문에 일반적으로는 연산에 사용할 데이터가 위치한 레지스터 혹은 메모리의 주소값이 포함되어 있다.

즉, Operand에 어떤 주소가 담기는지에 따라 Addressing Mode가 달라진다.

## Immediate addressing mode

- 연산에 사용할 데이터가 Operand에 직접 포함되는 방식
- 추가적인 접근이 필요 없기 때문에 데이터 접근 속도가 매우 빠름.
- 단, 표현할 수 있는 데이터의 크기가 제한적임

## Direct addressing mode

<img width=50% src="https://github.com/user-attachments/assets/08b24344-dfde-4910-88c2-209af242c6b0">

- Operand 필드에 연산에 사용될 데이터의 메모리 주소(유효 주소)를 명시하는 방식
- Immediate addressing mode 보다 표현할 수 있는 데이터의 크기가 더 크다는
- 단, Operand 수가 증가할 수록 결국 표현할 수 있는 메모리 주소 범위가 줄어든다.

## Indirect addressing mode

<img width=50% src="https://github.com/user-attachments/assets/029d5443-7ba2-4b7b-ba02-10e8c4e9f016">

- Operand 필드에 유효 주소의 주소를 명시하는 방식
- 메모리에 접근해 유효 주소의 포인터를 가져와 최종적으로 해당 포인터를 통해 연산에 사용할 데이터에 접근하는 방식
- 더 넓은 범위의 메모리 주소 표현이 가능. 단, 메모리에 두번 접근해야하기 때문에 느림.

추가로, 더 빠른 접근을 위해 메모리 주소 대신 레지스터의 주소를 사용해 최종적으로 데이터를 가져오는 방식인 `Register addressing mode`와 `Register Indirect addressing mode` 방식이 있다. 

`Register Indirect addressing mode`의 경우 기존 `Indirect addressing mode`의 문제점인 메모리 접근 횟수를 한번으로 줄일 수 있다.

## Displacement addressing mode

`Displacement addressing mode`는 특정 레지스터의 값과 Operand를 더해 유호 주소를 찾는 방법이다. 

어떤 레지스터의 값을 사용하냐에 따라 Relative와 Base-Register addressing mode 모드로 나뉜다.

- Relative: PC + Operand
- Base-Register: Base-Register + Operand

# 명령어 파이프라인

여러 명령어를 효율적으로 처리하기 위해 CPU는 명령어를 병렬적으로 처리한다. 하지만, 무조건 병렬적으로 처리할 수 있는 것은 아니며, 명령어 처리 과정은 `인출, 해석, 실행, 결과값 저장` 단계가 있다.

![3](https://github.com/user-attachments/assets/5b55efbb-6b52-43a0-9076-57e9e32aa36d)

각 단계가 겹치지 않는다면, CPU는 각 명령어를 병렬적으로 처리할 수 있다. 이를 명령어 파이프라인이라고 한다.

이와 같이 명령어 파이프라인 기법을 사용하면, 짧은 시간 동안 많은 명령어를 처리할 수 있다. 하지만, 특수한 상황에서 효율적으로 처리하지 못하는 문제가 발생하는데 이를 `Pipeline hazard`라고 한다.

## Pipeline hazard

- Data hazard
    - 명령어간 데이터 의존성이 존재할 경우 발생
    - 예를들어, 이전 명령어의 결과값이 다음 명령어의 피연산자로 사용될 때
    - Data hazard가 발생한 경우 앞선 명령어의 결과값이 저장될 때 까지 다음 명령어를 실행할 수 없음
- Control hazard
    - PC가 Call 명령어 혹은 인터럽트로 인해 새로운 값으로 초기화 되었을 때
    - 예를들어, `명령어 1`에 Call 연산 코드가 포함되어 있으면, 사실상 다음 명령어를 처리하는 것이 의미가 없음
    - 위 문제를 해결하기 위한 기술 중 하나로 `Branch prediction`가 사용됨
- Structural(or Resource) hazard
    - 병렬적으로 처리되는 명령어가 동시에 ALU 혹은 레지스터와 같은 자원에 동시에 접근하려 할때 발생

> [!NOTE]
> 🤔 **Branch prediction란?**
> 
> Branch Prediction(분기 예측)은 CPU 파이프라인의 효율성을 높이기 위한 기술이다.
> 
> 분기 예측은 특정 명령어에 의해 분기가 발생하면, 그 도착지를 BTB(Branch Target Buffer)에 기록한 후 동일한 명령어가 실행됐을 때 도착지에 해당하는 명령어를 미리 파이프라인에 올려 선능을 향상 시킨다.
> 
> 또한, ****BHT(Branch History Table) 비트를 활용해 taken(점프한 경우) 혹은 not taken(점프하지 않은 경우)을 기록한다.
> 
> 만약, 2비트의 예측기를 사용하는 CPU의 경우, 네 가지 상태(weakly taken, weakly not taken, strongly not taken, strongly taken)를 기록해 이후 동일한 명령어가 실행됐을 때 예측도를 높인다.

## 슈퍼스칼라

슈퍼스칼라는 CPU 내부에 여러 개의 명령어 파이프라인을 구조를 뜻한다. 현대의 CPU는 슈퍼스카라 구조를 띄고있다.

![4](https://github.com/user-attachments/assets/57ed99c2-4610-4abf-b159-3a8265477d6f)

슈퍼스칼라 구조로 명령어 처리가 가능한 CPU를 슈퍼스칼라 프로세서(or CPU)라고 한다. 

CPU는 많은 파이프라인을 가질 수록 이론상 성능이 배로 증가해야하지만, 파이프라인의 수가 늘어날 수록 `Pipeline hazard` 발생 확률이 상승함으로 결과적으로 배로 증가하지는 않는다.

## Out-of-Order Execution(OoOE)

OoOE는 명령어들을 순차적으로 실행하는 것이 아닌 `Data hazard`에 의해 다음 명령어를 기다리지 않고, 의존성이 없는 명령어를 먼저 실행하는 기법이다. 때문에 OoOE 처리가 가능한 CPU는 명령어들의 데이터 의존성을 가지고 있는지를 파악할 수 있어야한다.

# Instruction Set Architecture(ISA)

CPU는 종류(제조사)에 따라 처리할 수 있는 명령어의 종류와 모양새, 크기가 조금씩 다르다. 각 CPU마다 처리할 수 있는 명령어의 집합을 ISA라고한다.

대표적으로 인텔이 CPU의 `x86-64 ISA`, `ARM ISA`가 있다.

## Complex Instruction Set Computer(CISC)

CISC는 의미대로 복잡한 명령어를 처리하는 컴퓨터(CPU)를 뜻한다. 때문에 ISA도 그에 따라 복잡한 연산을 처리할 수 있도록 구성되어 있다. 대표적인 CISC 기반의 ISA가 `x86-64 ISA`다.

CISC의 ISA는 복잡한 명령어를 처리해야하기 때문에 지원하는 명령어의 수가 많고, 기능 또한 다양하다. 추가로 **가변 길이 명령어**를 활용하며, 독특한 주소 지정 방식들이 존재한다.

또 다른 특징으로 CISC는 같은 동작을 RISC보다 **적은 수의 명령어**로 처리할 수 있어 컴파일 시 생성되는 실행파일의 크기가 작다. 때문에 CISC는 메모리 사이즈가 제한적을 때 메모리를 최대한 아끼며 개발해야 했던 시기에 인기가 많았다. 

하지만, 가변 길이 명령어를 사용하기 때문에 명령어 파이프라인이 효율적으로 명령어를 처리할 수 없다는 치명적인 단점이 있다.

```swift
| 인출 |      해석      | 실행 | 저장 |
      | 인출 | 노는 공간 | 해석 | 실행 | 저장 |
```

때문에 현대 CPU에서는 마이크로오퍼 변환, 마이크로오퍼 캐시 등 다양한 최적화 기술을 통해 이 단점을 보완하고 있다.

> [!NOTE]
> 🤔 **마이크로오퍼(micro-op) 변환 & 캐시 란?**
> 
> - 마이크로오퍼 변환
>     - 가변 길이 명령어를 고정 길이의 마이크로오퍼로 분해하여 내부 파이프라인에 전달하는 방식
>     - 고정 길이 명령어를 처리하는 RISC 방식에 가까운 형태로 변환하여 파이프라인 효율성을 개선
> - 마이크로오퍼 캐시
>     - 이미 디코딩된 명령어들을 캐시에 저장해 두었다가 재사용
>     - 캐시를 통해, 동일한 명령어를 다시 디코딩하는 부담을 감소

추가로, CISC 경우 많은 명령어를 지원하지만, 실제로는 자수 사용되는 명령어만 쓰인다. 대다수의 복잡한 명령어는 잘 사용되지 않는다.

## Reduced Instruction Set Computer(RISC)

RISC는 CISC 비해 명령어의 종류가 적고 고정 길이 명령어를 사용하며, 되도록 1클럭 내외로 처리되는 명령어를 지향한다. 때문에 명령어 파이프라이닝에 최적화되어 있다.

또한, 메모리에 접근하는 명령어를 load, store 두 개로 제한할 만큼 메모리 접근이 단순하고 최소화되어 있다. 때문에 CISC 보다 주소 지정 방식 종류가 적다.

대신 레지스터를 적극활용해 연산을 처리하기 때문에 범용 레지스터의 개수가 많다.

`ARM`이 대표적은 RISC 기반의 ISA다.
