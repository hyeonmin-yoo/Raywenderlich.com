*This article/tutoral is permitted translation in Korean language by raywenderlich.com*

*이 글/튜토리얼은 raywenderlich.com의 **허가를 받고 번역**하였습니다.*

*전문 번역가가 아니므로 **다소의 오역/의역 혹은 과감한 생략**이 있을 수 있음을 알려드립니다.*

-----

# Swift 5.2 버전에서 달라진 것들

[What’s New in Swift 5.2](https://www.raywenderlich.com/9224115-what-s-new-in-swift-5-2) by [Bill Morefiled](https://www.raywenderlich.com/u/bmorefield)

* **Version**:  Swift 5, iOS 13, Xcode 11
* **Level**: Intermediate
* [**Download Materials**](https://koenig-media.raywenderlich.com/uploads/2020/04/new-swift52-1.zip)

Swift 5.2는 Xcode 11.4이 판올림 됨에 따라 이용가능하게 되었습니다. 이 글은 스위프트 최신 버전에서 보게 될 변경사항 혹은 개선사항에 대해서 대략적으로 설명합니다.
전반적으로 Swift 5.2는 작은 판올림 버전(Minor release)로서 Swift 개발자의 작업에 도움이되는 많은 변경사항과 약간의 개선사항이 있습니다. 이번 판올림에서의 변경/개선 사항은 아래와 같습니다.

* 보다 유용한 에러 메세지 (Error message)를 통한 더 나은 에러 진단, 특히 SwiftUI
* 특정 작업을 단순화하는 새로운 기능
* 주요 버그 수정

아래의 링크를 참조하시면 Swift 발전 전략(Swift Evolution proposals)이나 Swift 버그 보고서(Swift bug reports)등을 더 자세히 살펴볼 수 있습니다. 이 튜토리얼을 진행하기 위해 Playground를 사용합니다. 또, 제공된 Playground 파일을 (튜토리얼 연습용, 최종 완성판) 다운로드 할 수 있습니다. 먼저 가장 중요한 기능인 오류 메시지 개선을 살펴 보겠습니다.

## 향상된 진단 및 오류 메시지, Improved Diagnostics and Error Messages

코드에 오류가 있으면 컴파일러는 그 오류와 위치에 대한 보다 정확한 설명을 제공하게 바뀌었습니다.

```swift
var str = "10"
let total = str + 5
```
오류 메시지는 '+' 연산자를 사용하여 String 타입과 Int 타입을 연산할 수 없다는 에러 메시지를 표시했습니다 (+ 연산자 아래의 붉은색 언더바). 또한 문제의 원인을 에러메세지(Binary operator…)로 표시했습니다.. 문자열로 표시된 숫자에 5를 추가하는 것이 원래의 의도라면 다음과 같이 오류를 해결할 수 있습니다.

```swift
var str = "10"
let total = Double(str)! + 5
```
그러나 이전까지의 버전에서는 오류 메시지가 그다지 도움이 되지 못했습니다. 특히, 자료형 체크(Type checking)와 관련된 오류에서 그랬습니다. 이러한 문제를 Swift 5.2에서는  자료형을 체크를 개선하는 것으로 이 문제를 해결했습니다.

### 더 찾기 쉬워진 오류, Easier Troubleshooting

SwiftUI에 관한 아래와 같은 코드가 있다고 할 때,
```swift
struct SquareView : View {
  @State var angle = 0.0
  
  var body: some View {
    VStack {
      TextField("Angle:", text: $angle)
      Rectangle()
        .rotation(Angle(degrees: angle))
        .frame(width: 100.0, height: 100.0)
    }
  }
}
```
Xcode 11.4 이전의 오류 메시지는 다음과 같았습니다.


![alt text](https://koenig-media.raywenderlich.com/uploads/2020/03/unclear-sui.png)
*이전 버전의 Swift의 경우에는 이해하기 조금 애매모호한 경우가 있었습니다*



SwiftUI로 작업 할 때 이와 같은 분명하지 않은 오류 메시지가 표시되는 것이 일반적이었습니다. 이로 인해 SwiftUI 사용법을 배우기가 훨씬 어려웠고, 단순한 오타 조차도 실제 오류 찾아내기 위해서는 전체 코드를 장황하게 다시 읽어들이고, 삭제하고 주석 처리하는 과정이 반복되었습니다. 코드 블록을 다시보십시오. 실제 문제를 파악할 수 있습니까? 사실, Double을 CGFloat로 변환하는 것과는 아무런 관련이 없는 에러 메세지 입니다.

이제 starter 폴더에서 swiftui.playground 파일를 여십시오. 컴파일러는 훨씬 유용하고 실행 가능한 오류 메시지를 제공합니다.


￼![alt text](https://koenig-media.raywenderlich.com/uploads/2020/03/better-sui.png)
*Swift 5.2에서의 개선된 에러메세지*

개선된 오류 메시지는 오류가있는 위치(라인)를 정확히 가리키고 무엇이 문제인지를 알려줍니다. 바인딩(Binding)된 Double을 TextFile() 메소드가 필요로하는 파라메터, 즉 바인딩된 String 타입으로 변환할수 없다는 내용입니다.


어떤 부분이 문제인지 정확한 파악이 가능해 졌으므로, 위 오류는 아래와 같이 해결 할수 있습니다.

```swift
TextField("Angle", value: $angle, formatter: NumberFormatter.decimalFormatter)
```


### SwiftUI 이외, Not Just SwiftUI

더 나은 오류 메시지는 SwiftUI에서 가장 많이 볼 수 있지만 다른 Swift 코드도 개선되었습니다. Fianl 프로젝트에서 swift52.playground를 열면 주석 처리된 다음과 같은 코드를 볼수 있습니다.

```swift
let x: [Int] = [1, 2, 3, 4]
let w: UInt = 4

let filtered = x.filter { ($0 + w)  > 42 }
```

이 코드는 타입 형변환(Type casting)없이 UInt에 Int를 추가하려고 시도하며 컴파일 되지 않습니다. Swift 5.2 이전에는 컴파일러에 다음 오류가 표시되었습니다.

```swift
error: binary operator '+' cannot be applied to operands of type 'Int' and 'UInt'
```

이제 코드를 주석 해제하면 아래와 같이 Swift 5.2의 보다 정확하고 유용한 오류 메시지가 표시됩니다.

```swift
error: new-features.playground:34:22: error: cannot convert value of type 'UInt' to expected argument type 'Int'
_ = x.filter { ($0 + y) > 42 }
^
Int( )
```

> 진단 엔진(Diagnostic engine)의 개선 사항에 대한 자세한 설명은 이 블로그 [New Diagnostic Architecture Overview](https://swift.org/blog/new-diagnostic-arch-overview/)를 참조하십시오.

## 추가된 문법적 편의 사항, Syntactic Sugar Additions
스위프트는 이미 꽤 많은 문법적 편의 사항들을 제공하고 있습니다. 이번 5.2버전에서는 개발자들이 아주 편리하다고 느낄수 있는 두 가지 새로운 기능이 추가되었습니다. 첫째는, 함수로서의 호출 타입 (Calling types as functions) 둘째는, 함수로서의 Key path 표현식(key path expressions as functions) 입니다.

> **Syntactic sugar**란 언어의 문법을 보다 쉽고 간결하게 이해할 수 있도록 하는 추가 사항들을 의미합니다.

### 함수로서의 호출 타입, Calling Types as Functions
이 새로운 기능은 Swift에 정적으로 호출가능한 값을 소개합니다. 이게 무슨 의미일까요? 더 구체적으로 말하면, 클래스(Class)나 구조체(Structure)를 함수처럼 호출할수 있다는 뜻입니다.

### 더 간결해진 문법, Cleaner Syntax
이 새로운 기능은 Swift에 정적으로 호출가능한 값을 소개합니다. 이게 무슨 의미일까요? 더 구체적으로 말하면, 클래스(Class)나 구조체(Structure)를 함수처럼 호출할수 있다는 뜻입니다.

### 머신러닝 어플리케이션, Machine Lerning Application
이 개선사항은 특히 머신 러닝에 중점을 둔 것으로 보입니다. 원문에서 상세하게 언급한 것과 같이, 신경망 응용프로그램(Neural Network Applications)을 위한 문법(Syntax)을 정리했으고 이는 파이썬과 유사하며 상호 호환됩니다. 이를 통해, 좀 더 ML(Machine Lerning) 개발자에게 적합 해졌습니다.
> 더 자세한 사랑에 대해서는 GitHub 페이지를 참조하십시오. [SE-0253: Callable values of user-defined nominal types](https://github.com/apple/swift-evolution/blob/master/proposals/0253-callable.md)

### 함수로 이용 가능한 Key Path 표현식들 , Key Path Expressions as Functions
> Key Path Expressions란, Key 또는 KeyPath를 이용해서 간접적으로 데이터를 참조하거나 새로운 값을 할당하는 방법
스위프트 5.2에서는 `(Root) -> Value` 함수를 사용하고 있는곳 이라면 `\Root.value`를 사용할 수 있도록 되었습니다.
예를 들어,
```swift
orders.map { $0.email }
```
위와 같은 표현법을 이제는 아래와 같이 쓸수 있게 되었습니다.
```swift
orders.map(\.email)
```
위의 표현법은 오직 Key Path Literal Expressions으로 한정됩니다. 아래와 같은 밥법으로는 작성할 수 없습니다.
```swift
let kp = \.email
users.map(kp)
```
다만 아래와 같은 방법으로 유사하게 구현할수 있습니다.
```swift
let asEmail: (Order) -> String = \Order.email
orders.map(asEmail)
```
위의 두가지 개선 사항은 스위프트 언어를 더욱 함수 지향 프로그래밍 혹은 함수형 프로그래밍이 가능하도록 만듭니다. 이제 이 함수를, 호출한 블록(Blocks)이나 클로저(Closures)에서 사용하는 것이 가능합니다.

> 더 자세한 사항에 대해서는 GitHub 페이지를 참조하십시오.[SE-0249: Key Path Expressions as Functions](https://github.com/apple/swift-evolution/blob/master/proposals/0249-key-path-literal-function-expressions.md)

### 기본값 설정 가능해진 커스텀 Subscripts , Subscripts With Default Arguments
이제 커스텀 서브스크립트(Subscripts)의 전달인자(Argument)에 기본 값을 설정할 수 있게 되었습니다. 아래와 같은 곱셈을 위한 서브스크립트에서,
```swift
struct Multiplier {
  subscript(x: Int, y: Int = 1) -> Int {
    x * y
  }
}

let multiplier = Multiplier()
```
이번 개선으로 전달하지 않은 매개변수(Parameter)는 기본값으로 처리되므로, 아래와 같이 다양한 서브스크립드의 사용이 가능합니다.
```swift
multiplier[2, 3]
multiplier[4]
```
> Swift.org에서 더 자세한 내용을 볼 수 있습니다. [SR-6118: Mechanism to hand through #file/#line in subscripts](https://bugs.swift.org/browse/SR-6118)

## 주요 버그 개선 사항, Major Bug Fixes
지금까지는 Swift5.2에 추가된 기능과 개선사항에 대해 이야기했습니다. 여기서부터는 고쳐진 중요한 버그에 대해서 알아보겠습니다.

### 순서대로 호출 지연(Lazy) Filters, Lazy Filters are Called in Order
 Lazy 시퀀스(sequence)나 콜렉션(Collection)에서 Chaining으로 `filter(_:)`를 호출할 때, filtering predicates는 보다 바람직한 순서대로 출력됩니다. 하지만, 이 개선사항으로 인해 기존 코드를 고쳐야 할 필요가 있습니다. 대부분의 콜렉션에서 Swift는 아래와 같이 Filtering predicates를 순서대로 호출합니다
 ```swift
let array = ["1", "2", "3"]
let filtered = array
  .filter { _ in
    print("A")
    return true
  }
  .filter { _ in
    print("B")
    return true
  }

_ = Array(filtered)
 ```
결과는...
```swift
A
A
A
B
B
B
```
하지만 이전 버전의 스위프트의 **지연 시퀀스 또는 콜렉션**에서는 아래와 같이 역순으로 출력 되었습니다.
```swift
let lazyFiltered = array.lazy
  .filter { _ in
    print("A")
    return true
  }
  .filter { _ in
    print("B")
    return true
  }

_ = Array(lazyFiltered)
```

예상된(바람직한) 출력 결과:
```swift
A
B
A
B
A
B
```
Swift 5.2 이전의 출력 결과(버그):
```swift
B
A
B
A
B
A
```
Swift 5.2에서는 옳바르게 출력됩니다. 만약 이번 업데이트로 인해 결과 값이 반대로(예전의 방식으로) 출력된다면, 개선된 사항에 맞춰 코드를 수정할 필요가 있습니다.

> Swift.org에서 더 자세한 내용을 볼 수 있습니다. [SR-11841: Lazy filter runs in unexpected order](https://bugs.swift.org/browse/SR-11841)

이제부터 소개될 수정 사항(Bug fixes)들은 기존 코드에 미치는 영향이 적지만 주목할 가치가 있습니다. 만약 이전에 아래와 같은 버그를 경험한적이 있다면요.

### 내부함수에서 외부함수의 전달인자를 기본값으로 사용 가능 , Default Values From Outer Scopes
이제 Swift 컴파일러는 외부함수의 전달인자(Arguments)를 기본값(`default`)으로 하는 내부함수 전달인자를 지원합니다.
```swift
func outer(x: Int) -> (Int, Int) {
  func inner(y: Int = x) -> Int {
    return y
  }

  return (inner(), inner(y: 0))
}
func generatePointer() {
```
이전 버전에서 위의 코드는 크래쉬를 일으켰습니다.

> 이 수정 사항에 대해서는 Swift.org에서 더 자세한 내용을 볼 수 있습니다. [SR-2189: Nested function with local default value crashes](https://bugs.swift.org/browse/SR-2189)

### 허상 포인터(Dangling Pointers) 사용 시에 경고 메세지, Warning When Passing Dangling Pointers
이제, 유효한 객체를 가리키지 않는 포인터 전달인자(Pointer Argument)를 사용할 때, 컴파일러는 경고를 표시합니다.
```swift
func generatePointer() {
  var number: Int8 = 0
  let pointer = UnsafePointer(&number)
}
```
경고 메세지의 내용은...
```swift
warning: initialization of 'UnsafePointer<Int8>' results in a dangling pointer<int8>
```

### , Overridden Methods Can’t Use Incorrect Generics

### , Class-Constrained Protocol Extensions

### , Disambiguate Functions with Named Parameters

##  , Where to Go From Here
