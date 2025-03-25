일급객체
===



> 컴퓨터 프로그래밍에서 다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체를 가리킨다.

크리스토퍼 스트래치라는 컴퓨터 과학자가 ALGOL의 Number와 Procedure를 비교하는 과정에서 일급객체, 이급객체 라는 표현을 사용하며 알려졌다고 합니다. 

### 수학의 닫혀있다? 개념

- 자연수는 덧셈, 곱셈 연산에 대해 닫혀있다. 
- 정수는 덧셈, 뺄셈, 곱셈 연산에 대해 닫혀있다.
- 유리수는 사칙연산에 대해 닫혀있다. 

대수적 닫힘 (Algebraically Closed)

## 일급 객체의 조건 

1. 런타임에 객체가 생성될 수 있다
2. 함수의 파라미터로 사용될 수 있다.
3. 함수의 리턴 값으로 객체를 사용할 수 있다. 
4. 데이터 구조 안에 저장될 수 있다. (할당 명령문의 대상이 될 수 있다 or 변수/상수에 할당 가능하다)
5. 동일 비교의 대상이 될 수 있다. (5번은 잘 언급 안하는 것 같음)

### 런타임에 객체가 생성될 수 있다.

```swift
let numbers = [1, 2, 3, 4, 5]
let doubledNumbers = numbers.map { $0 * 2 } // 런타임에 클로저 생성 및 실행
print(doubledNumbers) // 출력: [2, 4, 6, 8, 10]

```
### 함수의 파라미터로 사용될 수 있다.

```swift 
func performOperation(on value: Int, using operation: (Int) -> Int) -> Int {
    return operation(value)
}

let square = { (number: Int) -> Int in
    return number * number
}

let result = performOperation(on: 5, using: square)
print(result) // 출력: 25

```
### 함수의 리턴 값으로 객체를 사용할 수 있다. 

```swift
func makeMultiplier(by factor: Int) -> (Int) -> Int {
    return { number in
        return number * factor
    }
}

let multiplyByThree = makeMultiplier(by: 3)
print(multiplyByThree(10)) // 출력: 30

```
### 데이터 구조 안에 저장될 수 있다. 

```swift
let greeting: (String) -> String = { name in
    return "Hello, \(name)!"
}

print(greeting("Alice")) // 출력: Hello, Alice!

```

### 동일 비교의 대상이 될 수 있다. 
ALGO에서는 따라 줘야 하는 조건이었던 것 같으나, Swift에서는 안되는 애들도 많은듯

## 이급객체, 삼급객체?
일급 객체의 조건을 모두 만족하지는 않는, 일부만 만족하는 경우에 2급으로 분류 하는 듯 함
일급객체 개념이 등장한 ALGO에서 procedure는 

## 다른 타입들은?
1. `class`
2. `struct`
3. `enum`
4. `Tuples`
5. `function`
6. `closure`
7. `Optional`

### 프로토콜은 1급 객체일까요?

> 타입캐스팅! 

#### 런타임에 생성 + 변수/상수에 할당 

```swift
let triangle: Shape
```

```swift
struct Triangle: Shape {
}

protocol Shape {
}
```

```swift
let triangle: Shape = Triangle()
```
#### 다른 조건들도 마찬가지
```swift
func scale(shape: Shape) -> Shape {
	return Triangle()
}
```



## 그래서 왜 중요하지?

#### 1. 코드 유연성 증가, 재사용성 증가

#### 2. 함수형 프로그래밍
##### 메소드 체이닝, 고차함수(map, reduce, filter)

```swift
array.filter { ... }.map { ... }
```

#### 3. 추상화 수준 향상

가독성 상승
```swift
let isEven = { (number: Int) -> Bool in
    return number % 2 == 0
}

let numbers = [1, 2, 3, 4, 5]
let evenNumbers = numbers.filter(isEven)

```

```swift
func processData(_ data: [Int]) -> Int {
    let filterStep = { (numbers: [Int]) -> [Int] in
        return numbers.filter { $0 > 0 }
    }
    
    let mapStep = { (numbers: [Int]) -> [Int] in
        return numbers.map { $0 * $0 }
    }
    
    let reduceStep = { (numbers: [Int]) -> Int in
        return numbers.reduce(0, +)
    }
    
    return reduceStep(mapStep(filterStep(data)))
}
```
#### 4. 동적 행동

런타임에 생성, 전달 가능 -> 네트워크 응답에 따라 동적으로 대응해야하는 iOS와 같은 상황에 유리
비동기 작업의 완료 후 코드를 동적으로 선택 가능.

```swift
func fetchData(completion: @escaping (Result<Data, Error>) -> Void) {
    // 데이터 fetching 로직
    // ...
    completion(.success(data))
}

fetchData { result in
    switch result {
    case .success(let data):
        print("Data fetched successfully: \(data)")
    case .failure(let error):
        print("Error fetching data: \(error)")
    }
}

```
#### 5. POP
