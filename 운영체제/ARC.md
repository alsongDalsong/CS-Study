
메모리를 사용하지 않는다고 판단 했을 때 자동으로 ARC를 통해 클래스 인스턴스를 메모리로 부터 해제함

## ARC란? 

## Garbage Collector vs ARC?

### ARC
컴파일 시점에 메모리 관리 코드를 적절한 위치에 자동 삽입.
- 런타임 시점에 추가적인 오버헤드가 발생하지 않는다.
- **순환참조(retain cycle)** 발생 시 메모리 누수의 위험이 있다.
  
### GC 
프로그램 실행중, **런타임에** 동적으로 감시 더 이상 사용할 필요가 없다고 여겨지는 것(더이상 어떠한 변수도 가리키고 있지 않은 메모리 영역)을 메모리에서 삭제함. 
- 개발자가 별도로 동적 객체를 관리할 필요가 없다.
- 인스턴스가 해제될 확률이 ARC에 비해 높다.
- 런타임 시점에 객체를 추적하는 과정에서 오버헤드가 발생하며, 이로인한 성능저하가 있을 수 있다.

## MRC

## Reference Count 확인하기 :  `CFGetRetainCount(_:)`
 
`CFGetRetainCount(_:)` 를 통해 해당 오브젝트의 RC를 확인할 수 있음. (import Foundation 필수)
[CFGetRetainCount Documentation](https://developer.apple.com/documentation/corefoundation/1521288-cfgetretaincount)


`CRGetRetainCount()` 함수가 강한참조로써 캡쳐를 수행하여 호출하면 RC가 예상치보다 +1 되어있음.
[출처](https://stackoverflow.com/questions/57452435/why-cfgetretaincount-logs-out-always-2-retain-count-for-new-instances)

#### 예시코드

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?

reference1 = Person(name: "John Appleseed")
reference2 = reference1
reference3 = reference1

print(CFGetRetainCount(reference1))
print(CFGetRetainCount(reference2))
print(CFGetRetainCount(reference3))

reference1 = nil
print(CFGetRetainCount(reference2))
reference2 = nil
print(CFGetRetainCount(reference3))
reference3 = nil
```

#### 실행 결과
<img width="379" alt="image" src="https://github.com/user-attachments/assets/6f3f1b2d-d2ff-4a56-87e2-ab9530f5da38" />


## Object 생명주기, Observable Object 생명주기

### Object의 생명주기

`init()` 으로 시작 되며 마지막 사용에서 끝난다. 

```swift
class Person {
    let name: String
    init(name: String) { self.name = name }
    var apartment: Apartment?
    deinit { print("\(name) is being deinitialized") }
}

class Apartment {
    let unit: String
    init(unit: String) { self.unit = unit }
		// 여기만 weak으로 선언해줬다.
    weak var tenant: Person?
    deinit { print("Apartment \(unit) is being deinitialized") }
}
```

![weak참조](https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5f15b5f3-7f03-46d3-8287-bee80efd6150%2FweakReference01_2x.png&blockId=620efa14-9b67-4b0e-a453-662abb64b929)


```swift
class Traveler {
    var name: String
    var account: Account?
    
    init(name: String) {
        self.name = name
    }
}

class Account {
    weak var traveler: Traveler?
    var points: Int
    func printSummary() {
        print("\(traveler!.name) has \(points) points")
    }
    init(traveler: Traveler, points: Int) {
        self.traveler = traveler
        self.points = points
    }
    deinit {
        print("\(self) deinit")
    }
}

func test() {
    let traveler = Traveler(name: "Lily")
    let account = Account(traveler: traveler, points: 1000)
    traveler.account = account
    
    account.printSummary()
}
print("test")
test()
```


## Auto Release Pool과 ARC

## Swift 컴파일러

### 바이너리 코드로 전환되는 과정

![과정](https://velog.velcdn.com/images%2Fdvhuni%2Fpost%2F9c2e6842-3049-4514-ad2e-5550eb113e0c%2FUntitled.png)

## AST, SIL

단계	옵션
1. 구문 분석	`swiftc -dump-parse`
2. 의미 분석	`swiftc -dump-ast`
4. SIL의 생성	`swiftc -emit-silgen`
5. SIL의 정규화	`swiftc -emit-sil`
7. LLVM IR의 생성	`swiftc -emit-ir`

### 구문분석 (AST)

<img width="1514" alt="image" src="https://github.com/user-attachments/assets/8627a42a-8f1b-4ec2-ac31-350c75473a8f" />

### 의미분석

이때 까지도 retain, release 코드는 삽입 안됨.
<img width="1840" alt="image" src="https://github.com/user-attachments/assets/e4d07142-e379-47c8-a251-acc2ee853cf3" />

### SIL 생성
여기서 부터 ARC 메모리 관리코드 삽입
<img width="1840" alt="image" src="https://github.com/user-attachments/assets/0bab1ffb-af11-4e44-9d82-d569f14ddaa1" />


##
