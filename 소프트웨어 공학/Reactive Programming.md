# 반응형 프로그래밍

## 참고...? (채용 우대사항)

- 많은 곳에서 반응형 프로그래밍 키워드가 많이 보입니다.
- Combine보다는 RxSwift라는 키워드가 좀더 많이 보이는 것 같습니다.

<img width="1200" src="https://github.com/user-attachments/assets/4eb4500d-72ee-40c3-ae2e-2a311186a7d0">

## 반응형 프로그래밍이란?

<img width="1200" src="https://github.com/user-attachments/assets/7252e21d-e0e0-416a-9067-76fe7355a2b4">

- In computing, reactive programming is a declarative programming paradigm concerned with data streams and the propagation of change.
- 데이터 스트림과 변화의 전파와 관련된 선언적 프로그래밍 패러다임... 무슨뜻일까요? 하나하나 간단하게 살펴봅시다.

### 간단한 의사코드

```
var b = 1
var c = 2
var a = b + c
b = 10
console.log(a) // 3 출력!
```

- 만약에 우리가 $= 라는 단순히 초기 계산뿐 아니라, 오른쪽 표현식에 포함된 변수들이 변경될 때 자동으로 재계산을 해주는 가상의 반응형 대입 연산자를 만든다고 해본다면

```
var b = 1
var c = 2
var a $= b + c
b = 10
console.log(a) // 12 출력!
```

- 데이터의 변화에 따라서 a가 12로 자동으로 업데이트되는 것을 볼 수 있습니다.
- 이렇게 b나 c가 바뀌게 된다면 a도 바꾸도록 하여라라는 $=를 추가해 줌으로써 반응형 프로그래밍이 되었습니다.
- $=는 무엇을 할 것인지 선언적 방식으로 정의했다고 합니다. b나 c가 바뀌면 어떻게 a가 업데이트 되는지 내부 구현은 보이지 않고, 나는 무엇을 원하는지만 정의했기 때문에 선언형 프로그래밍이라고 합니다.

> [!NOTE]
> 선언형 프로그래밍의 성격을 가지고 있으면서 데이터의 변경을 전파하고 반영하는 방식을 반응형 프로그래밍이라고 합니다.

### 데이터의 흐름

- 데이터 흐름, 변화의 전파라고 했는데 데이터 흐름이 변화의 전파가 아닐까요? 왜 이렇게 구분을 시켰는지 알아봅시다.
- 데이터 스트림이라는 키워드가 반응형 프로그래밍에서 아주 중요하게 작용합니다.

- 아래의 예시는 반응형 프로그래밍은 아니고, 데이터 스트림의 아주 간단한 예시입니다.
- 각 단계가 데이터의 변환 단계를 나타내고 한 흐름에 따라 처리되는 모습이 보입니다.

```JavaScript
// 배열에 2를 곱한 뒤 앞의 3개 요소 출력하기

Array.of(1,2,3,4,5)
  .map(x => x * 2)
  .slice(0, 3)
  .forEach(x => console.log(x))
```

### PULL 과 PUSH

- git을 사용해본 우리라면 아주 익숙한 단어라고 생각합니다.
- 반응형 프로그래밍은 기본적으로는 `PUSH` 방식으로 동작합니다.

- `PULL`은 데이터 외부 환경(데이터 생산자)에게서 소비자가 데이터를 달라고 하는 것입니다. 결국 데이터의 변경을 계속 물어봐야 한다는 것이다.

<img width="400" src="https://github.com/user-attachments/assets/d1cca9da-4c50-4502-99c3-ae07444adb7a" />

- `PUSH`는 데이터 생산자가 데이터를 주는 것입니다. 알아서 데이터가 변경되면 데이터 생산자가 소비자에게 데이터를 줄 수 있겠죠?

<img width="400" src="https://github.com/user-attachments/assets/df13ef30-eb40-4b2c-8b6c-c504f8b35ee4" />

- 제어권을 외부에 준다고 해서 제어권 역전이라는 용어로도 반응형 프로그래밍을 설명한다고 합니다.
- 하지만 이렇게 PUSH 방식으로 주기만 한다면 데이터 소비자 입장에서 데이터가 필요하지 않거나 데이터를 사용할 수 없는 상황이라면 어떨까요?
- 이때 발생하는 문제가 백프레셔(Backpressure) 문제입니다. 이러한 문제를 해결하기 위해서 처리 가능한 데이터 양만 요청하는 등 처리가 필요합니다.
- 실제 React는 반응형 프로그래밍 성격인 `PUSH` 방식으로 동작하는 것처럼 보이지만 내부적으로는 `PULL` 방식으로 동작한다고 합니다.

## 반응형 프로그래밍의 등장 배경

- 반응형 프로그래밍은 크게 두가지 문제를 해결하기 위해서 나오지 않았을까요?

### 복잡한 데이터 구조와 흐름

- 간단한 웹사이트만 보더라도 수없이 많은 Input, Output으로 이루어져 있습니다. 처리해야 하는 데이터가 많아지면서  데이터의 변경이 발생할 때마다 어떠한 UI를 바꿔주거나 어떠한 행동을 해야하는지 관리해줄 필요가 생겼습니다.

### 비동기 프로그래밍

- 컴퓨터의 처리가 발전하면서 다양한 비동기 작업들이 많아지면서 전통적인 명령형 방식은 콜백 지옥이나 상태관리의 복잡성으로 문제가 생겨 이를 해결하고자 반응형 프로그래밍이 나왔다고 합니다. 데이터 스트림과 연산자 체인을 통해 비동기 프로세스를 관리할 수 있습니다.

## ReactiveX

- ReactiveX(Rx, Reactive Extensions)는 마이크로소프트에서 반응형 프로그래밍을 구현하기 위해 만든 라이브러리 집합으로 2011년에 공개된 오픈소스 프로젝트이다. 그만큼 역사와 전통을 가지고 있는... 라이브러리라고 할 수 있다. RxSwift의 경우 2015년에 첫번째 Release Note를 확인하실 수 있습니다.
- [ReactiveX 홈페이지](https://reactivex.io/)를 살펴보면 어떠한 철학을 가지고 있고, 어떠한 특성이 있는지 확인해보실 수 있습니다.

<img width="800" src="https://github.com/user-attachments/assets/5a50f928-9851-44a6-bdfb-9ca55299fdba">

- 함수형, 코드의 간결성, 효과적인 에러 핸들링, 동시성 프로그래밍에 효과적이라고 합니다.

<img width="800" src="https://github.com/user-attachments/assets/b33fca19-1485-4e7e-bf19-ebb64601eb96">

- ReactiveX는 Observable 시퀀스를 사용하여 비동기 및 이벤트 기반 프로그램을 구성하는 라이브러리라고 소개되어 있습니다. 여기서 주의깊게 봐야할 키워드는 observable sequence, Asynchronous, event-based 입니다.

## 간단한 실습?

- ReactiveX는 RxSwift를 통해 Swift를 지원하는데요.
- 아래와 같이 검색을 하게 되면 검색 버튼을 눌렀을 때 뿐만이 아니라 텍스트의 변화에 따라 자동으로 검색을 해주는 앱을 만든다고 생각해봅시다.

<img width="250" src="https://github.com/user-attachments/assets/a0ec7fd7-be5a-427d-8d66-28d44f8bb947">

- 어떤 방법이 떠오르시나요? `UISearchBarDelegate`를 사용해서 텍스트가 `textDidChange` 일때... 데이터를 받아서 어쩌구 저쩌구...
- 이 방법을 RxSwift와 Combine은 너무 쉽게 구현이 가능합니다.

### RxSwift

- 아래의 방식으로 내부적으로 searchBar.rx 의 뒤에 여러가지를 붙일 수 있게 구현된 것 같습니다. 

```swift
        /// 텍스트의 변경시 이벤트
        let searchBarText = searchController.searchBar.rx.text.orEmpty
            .debounce(.seconds(1), scheduler: MainScheduler.instance)

        /// 검색 버튼 클릭시 이벤트
        let searchButtonClick = searchController.searchBar.rx.searchButtonClicked
            .withLatestFrom(searchController.searchBar.rx.text.orEmpty)

        /// 두 이벤트를 합침
        Observable.merge(searchBarText, searchButtonClick)
            .distinctUntilChanged()
            .flatMapLatest(fetchBooks)
            .observe(on: MainScheduler.instance)
            .bind(to: searchResultsController.searchResultsRelay)
            .disposed(by: disposeBag)
```

이렇게 bind를 해준다면 이 데이터를 받는 곳에서는 아래와 같이 값이 변경된다면 reloadData를 해주는 방식으로 구현할 수 있습니다.

```swift
        /// 값이 변경되면 reloadData()
        searchResultsRelay
            .bind { [weak self] _ in self?.tableView.reloadData() }
            .disposed(by: disposeBag)
```

### Combine

이 방식이 똑같이 Combine으로는 어떻게 구현될까요? 진짜 비슷한 것 같습니다. 차이를 보자면 Scheduler, DisposeBag 정도...? bind가 sink로 바뀐 정도?

```swift
        searchTextSubject
            .debounce(for: .seconds(1), scheduler: DispatchQueue.main)
            .removeDuplicates()
            .flatMap { self.fetchBooks(query: $0) }
            .receive(on: DispatchQueue.main)
            .sink { self.searchResultsController.searchResultsSubject.send($0) }
            .store(in: &cancellables)

extension SearchTableViewController: UISearchBarDelegate {
    func searchBar(_ searchBar: UISearchBar, textDidChange searchText: String) {
        searchTextSubject.send(searchText)
    }
    
    func searchBarSearchButtonClicked(_ searchBar: UISearchBar) {
        guard let searchText = searchBar.text else { return }
        searchTextSubject.send(searchText)
    }
}
```

받는 곳에서는 아래와 같이 됩니다.

```swift
        searchResultsSubject
            .receive(on: DispatchQueue.main)
            .sink { _ in self.tableView.reloadData() }
            .store(in: &cancellables)
```

## 써보고 난뒤...

- 솔직히 써보고 난 뒤 차이점을 솔직히 잘... 설명할 수 없을 것 같습니다. 차이점을 설명하기에는 너무나도 저의 경험치가 낮습니다.
- 아무래도 알고 쓴다면 RxSwift에서 조금더 편리(?)하게 사용할 수 있을 것 같다는 느낌이 듭니다. SearchBar의 경우 delegate를 해줄필요 없이 그 역할도 해준다는 느낌이 들었고, 많은 기간동안 이러한 것들이 많이 발달되어 있을 것 같습니다.
- 코드스쿼드에서 박보영 님이 발표하신 것이 있는데... [RxSwift to Combine(박보영)](https://www.youtube.com/watch?v=Y273NDkYBg4)

### 차이

|Combine|RxSwift|
|--|--|
|Publishers/Subscribers|Observable/Observer|
|Cancellable|Disposable|
|PassthroughSubject|PublishSubject|
|x|ReplaySubject|
|CurrentValueSubject|BehaviorSubject|

- `Publisher`의 경우 `AnyPublisher<String, Error>`로 되어있지만 `Observable<String>`으로 되어있어 `Result<Sting, Error>`로 넣어줘야 한다고 합니다.
- `RxSwift`의 경우 `rx.viewWillAppear`처럼 이벤트를 관리할때 편하게 다양한 방법들이 지원됩니다.

### 속도

- 퍼포먼스 면에서는 Combine이 역시 First Party의 위엄을 보여줍니다... 물론 아래글들은 2019년 글들입니다.
- [미디엄 블로그 글](https://medium.com/@M0rtyMerr/will-combine-kill-rxswift-64780a150d89)

|속도|용량|
|--|--|
|<img src="https://github.com/user-attachments/assets/6ce0ceac-ef6b-463a-9534-3d17cb97677b" />|<img src="https://github.com/user-attachments/assets/9f3d9fa0-3c40-4a65-a7c4-3142762313e6" />|

- [외국 사이트 아티클](https://quickbirdstudios.com/blog/combine-vs-rxswift/)
- [퍼포먼스 비교 레포](https://github.com/QuickBirdEng/CombineRxSwiftPerformance)

<img width="860" src="https://github.com/user-attachments/assets/debaf507-9649-4194-b5be-4053ccd8df94" />
