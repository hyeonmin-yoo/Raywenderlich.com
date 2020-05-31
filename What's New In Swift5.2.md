> *This article/tutoral is permitted translation in Korean language by raywenderlich.com*

> *이 글/튜토리얼은 raywenderlich.com의 허가를 받고 번역하였습니다.*

> *전문 번역가가 아니므로 다소 오번역이 있을 수 있음을 알려드립니다.*

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

### 더 쉬운진 문제 해결, Easier Troubleshooting

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

### 머신러닝 어플리케이션 기능, Machine Lerning Application

###  , Key Path Expressions as Functions

###  , Subscripts With Default Arguments

## 주요 버그 개선 사항, Major Bug Fixes

### , Lazy Filters are Called in Order

### , Default Values From Outer Scopes

### , Warning When Passing Dangling Pointers

### , Overridden Methods Can’t Use Incorrect Generics

### , Class-Constrained Protocol Extensions

### , Disambiguate Functions with Named Parameters

##  , Where to Go From Here
