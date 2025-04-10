
유한상태기계, FSM
====

## 유한상태기계(Finite State Machine, FSM)란?

유한한 '상태'를 가지며, 입력에 따라 상태가 변화 하는 추상적인 계산 모델

## FSM의 구성요소
![IMG](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fx4VuD%2Fbtq22EVo2dp%2FmdhKVKE4dvkh1DnS4h7kY0%2Fimg.png)
### 상태(State)
시스템이 가질 수 있는 특정 조건. $`Q`$로 표기. 다이어그램에서 원으로 표시함
### 초기 상태(Initial State)
시스템의 시작 상태 $`q_0`$ 으로 표기함. 다이어그램에서 화살표를 받는 원으로 표시
### 입력(Input): 상태 변화를 유발하는 외부 신호.
입력값들의 집합. $`∑`$로 표기
### 전이(Transition): 한 상태에서 다른 상태로의 변화
다이어그램에서 화살표로 표시. 𝛿라는 함수로 표현함 
### 최종상태(Final State)
최종상태(final state)의 집합. $`F`$로 표기. 다이어그램에서 겹쳐진 원이 FinalState


## FSM 표시 방법

### 다이어그램, 상태표

![image](https://github.com/user-attachments/assets/b6e1acfe-2905-4fe0-abff-0c5f946f1ca3)


## FSM 예시

### 도어락

비밀번호가 1234 라고 치면

![IMG_8914](https://github.com/user-attachments/assets/862405fa-1ec3-4c69-b1b7-9d2463d74a53)

### 슈퍼마리오 

공격당하면 어떻게 변하는지도 함께 생각해보기

![슈퍼마리오](https://www.ashishvishwakarma.com/GoF-Design-Patterns-by-Example/State-Pattern/assets/mario-finite-state-machine.jpg)

### 계수기
<img width="400" src="https://github.com/user-attachments/assets/14253ae9-b211-4628-b369-f85618414d99">


### CPU
![image](https://github.com/user-attachments/assets/c08211c0-5d9d-435f-b292-4597a22b773b)

## 활용?

### 컴파일러의 렉서(Lexer), 파서(Parser)
![image](https://github.com/user-attachments/assets/4c2335bf-c5fb-4238-b1c5-b76e50c5fdac)

정규 언어, 문맥자유문법(Context-free grammar) -> 이를 통한 구문 트리 생성 

### 자연어 처리 (NLP)


## 소프트웨어 개발자로서의 활용

### Test Code, Test PipeLine

사용자의 사용 **시나리오**를 FSM로 표현하여 상태와 전이에 따라 명확하게 표현할 수 있음. 테스트 효율성 상승. 

### 비동기 작업 관리

비동기 작업의 진행 상황 추적에 활용 가능

대기중, 업로드/다운로드 중, 완료, 실패 와 같은 상태로 구분지어 상태를 정의 하고 event에 따라 transition을 일으켜 명확하게 표현 가능.

### 입력 폼 검증 (= 정규표현식)

입력 데이터가 유효한 데이터인지 확인

### UI 상태전환

로딩 및 네비게이션 흐름 제어 


> 오토마타는 기본적으로 복잡한 상태 전환 논리를 명확하게 모델링 하는데 쓰임. 복잡한 체계를 나누어 생각할 수 있음 -> 코드 책임 분리 용이, 유지 보수성 향상

