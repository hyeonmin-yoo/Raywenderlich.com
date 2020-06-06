- *This article/tutoral is **permitted translation in Korean language by raywenderlich.com***

- *이 글/튜토리얼은 raywenderlich.com의 **허가를 받고 번역**하였습니다.*

- *전문 번역가의 번역이 아니며 **다소의 오역/의역 혹은 과감한 생략**이 있을 수 있음을 알려드립니다.*

-----

# Swift 5.2 버전에서 달라진 것들

[SwiftUI Tutorial for iOS: Creating Charts](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts) by [Bill Morefiled](https://www.raywenderlich.com/u/bmorefield)

* **Version**:  Swift 5, iOS 13, Xcode 11
* **Level**: Intermediate
* [**Download Materials**](https://koenig-media.raywenderlich.com/uploads/2019/12/WeatherChart.zip)

차트는 유저에게 데이터를 보여주는 훌륭한 방법입니다. 상호연관된 수 많은 정보를 차트를 통해 이해할 수 있습니다. 현재의 관심을 끄는 트렌드를 보여주고, 원인과 이유를 분명히 하며, 시각화된 정보를 통해 유저가 쉽게 이해하도록 돕습니다.

이 SwiftUI 튜토리얼에서는, 여러분이 앱 데이터의 시각화를 위하여 어떻게 다양한 커스텀 차트를(Custom Charts) 만드는지 배웁니다.

아직 SwiftUI가 차트를 만드는 라이브러리(Native Chart Livrary)를 제공하지는 않지만, 뛰어난 그래픽 관련 라이브러리를 사용해 커스텀 차트를 만들수 있습니다. 이 튜토리얼에서 여러분은, *Great Smoky산 국립공원* 근처의 몇몇 기상관측소를 위해 기록적인 날씨 데이터를 이용하여 차트를 생성하고 앱에 추가하게 됩니다.

## 왜 차트를 사용하는가?, Why Use a Chart?
소수의 몇 가지 데이터를 살펴보며 정보를 팔악할 수는 있지만, 끝 없이 긴 숫자를 보는 것은 데이터에서 통찰을 얻기 위한 좋은 방법은 아닙니다. 수 많은 숫자를 보며 어떤 달이 얼마나 따뜻했는지, 어떤 달이 가장 건조했는지를 파악하는 것은 쉽지 않습니다.

대부분의 사람들은 정보가 그래픽으로 표시될 때 더 쉽게 그 정보에 대해서 파악합니다. 차트는 그래픽으로 표현하여 보는 사람에게 정보를 제공해 줍니다.

### 차트 vs. 그래프, Charts vs. Graphs

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/hypercycloid.png" width="394" height="363">
</p>

차트는 이야기를 해야합니다. 보는 사람으로이 해당 테이터를 이해하기 쉽고, 해석하기 쉽게 해야합니다. 즉, 모든 차트는 그래프이지만, 모든 그래프가 차트는 아닙니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/Screenshot-2019-11-10-18.23.29.png" class="center" width="483">
</p>

## 시작하며..., Getting Started

이 페이지의 시작 부분에 위치한 다운로드 링크를 통해 이 튜토리얼에 필요한 자료(Xcode project)를 다운로드 할수 있습니다. 빌드(Build)-런(Run)을 통해 테스트 해 보시길 바랍니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/station-starter-1.png" width="231">
</p>

이 앱은 기상관측소 다섯 곳의 데이터를 보여줍니다.

* Cherokee, NC와 Gatlinburg, TN: 이 두 도시는 공원을 지나는 *메인* 도로(main road)에 위치해 있습니다.
* Newfound Gap: *메인* 도로가 지나는 
* Townsend 5 S: 공원의 남서쪽 부분
* Mount LeConte: 공원 내에서 산의 가장 높은 곳들 중 한 곳

이 자료들은 각 지점 매일 매일의 강우량, 강설량, 온도 변화를 포함하고 있습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/leconte-starter.png" width="231">
</p>

각 지점을 탭하면 해당 지점에 대한 위치와 날씨 정보(하루 온도 변화, 한달 동안의 강우량, 일일 강설량)를 보여줍니다. 

강우량 데이터를 (세로)막대 차트(bar chart)에 추가하는 작업부터 시작하겠습니다.

## Refactoring for Charts

막대 차트에서 막대(bar)는 각 데이터 요소를 표현합니다. 각 막대의 길이는 수치적인 값을 나타내며 필요에 따라 수직적으로, 수평적으로 표현됩니다.

프로젝트 네비게이터에서 **Tabs** 그룹안의 **PrecipitationTab.swift** 파일을 여십시오. 55번째 라인에서 SwiftUI ```List()```를 볼수 있습니다. ```List()```는 0부터 11까지 반복하며 해당 연월과 총 강우량을 나타내고 표시합니다. ```List()``` 내부의 ```monthAbbreviationFromInt```, ```sumPrecipitation```함수는 각 달의 이름과 강우량 합계를 계산합니다.

**Chart**그룹에서 우클릭하여 새 파일(**New File**)을 만들겠습니다. User Interface 섹션에서 SwiftUI View를 선택해 **Next**를 누룹니다. 새로운 뷰의 파일명은 **PrecipitationChart**로 하겠습니다.

새로 만들어 지는 파일의 그룹이 **Chart**인지 확인한 후 **Creat** 버튼을 클릭 합니다. 이제 새로운 파일을 열어보면 Xocde의 우측에 Canvas가 보일것 입니다. 만약 보이지 않는 다면, 메뉴에서 **Editor ▸ Canvas**를 활성화 하십시오.

아래의 코드를 ```PrecipitationChart``` 구조체(Struct) 위에 추가합니다.
```swift
var measurements: [DayInfo]
```
이 변수의 안의 측정값(measurements)을 차트에 전달할 것입니다.이제 ```PrecipitationChart_Previews```를 업데이트하여 측정값을 프리뷰(preview)에 표시해 보도록 하겠습니다. 이 번에는 Mt. LeConte에서의 측정값을 넘겨주도록 합니다.
```swift
PrecipitationChart(measurements: WeatherInformation()!.stations[2].measurements)
```

먼저, 기존 함수를 현재의 뷰(PrecipitationChart) 복사합니다. 아래의 두 헬퍼(helper) 함수를 ```measurements```아래에 추가합니다.
```swift
func sumPrecipitation(_ month: Int) -> Double {
  self.measurements.filter {
    Calendar.current.component(.month, from: $0.date) == month + 1
  }.reduce(0, { $0 + $1.precipitation })
}

func monthAbbreviationFromInt(_ month: Int) -> String {
  let ma = Calendar.current.shortMonthSymbols
  return ma[month]
}
```
```sumPrecipitation(_:)```는 ```filter``` 고차함수(higher-order function)를 사용하여 오직 함수에 전달 된 월의 측정치만 가져옵니다. 1이 아닌 0에서 시작하므로 ```+ 1```로 조정해 줍니다. ```reduce```를 통해 강 강우량 데이터 값의 합을 구합니다.

```monthAbbreviationFromInt(_:)```함수는 전달인자```(_ month: Int)```와 일치하는 각 달의 이름(예: 1월 -> Jan)을 찾아 ```return```합니다.

다음으로 ```body```부분을 업데이트 하겠습니다.
```swift
List(0..<12) { month in
  Text("\(self.monthAbbreviationFromInt(month)): " +
    "\(self.sumPrecipitation(month))\"")
}
```

**PrecipitationTab.swift**를 열어 더 이상 필요치 않은 ```sumPrecipitation(_:)```와 ```monthAbbreviationFromInt(_:)```함수를 삭제하겠습니다. ```body``` 내부의 ```List```를  하여 새로운 뷰를 호출하기 위해 아래와 같이 교체하겠습니다.
```swift
PrecipitationChart(measurements: station.measurements)
```

> 앱을 실행(run)하여 결과를 보기 위해서는 시뮬레이터 화면에서 하나의 기상관측소를 선택한 후 강우량 탭(Precipitation tab)으로 설정해야 하는 부분에 주의하십시오.

## SwiftUI 막대(bar) 올리기 , Raising the SwiftUI Bar

SwiftUI는 몇가지 형태의 뷰(views)를 제공합니다. 바 차트는 ```Rectangle``` 형태호 만드는 것이 적당할 것입니다. ***PrecipitationChart.swift*** 팔일을 열고 ``` body``` 부분을 아래와 같이 바꿉니다.

```swift
// 1
HStack {
  // 2
  ForEach(0..<12) { month in
    // 3
    VStack {
      // 4
      Spacer()
      // 5
      Rectangle()
        .fill(Color.green)
        .frame(width: 20, height: CGFloat(self.sumPrecipitation(month)) * 15.0)
      // 6
      Text("\(self.monthAbbreviationFromInt(month))")
        .font(.footnote)
        .frame(height: 20)
    }
  }
}
````

코드에 관한 설명은 아래와 같습니다.
1. 수직 막대를 만들기 위해서는 ```HStack```을 사용하여 하위 뷰(child views)를 수평적으로 배열할 수 있습니다.
1. ```ForEach```문을 12번 반복하여 하위 코드를  실행합니다.
1. 하나 하나의 막대는 차트에 수직으로 쌓이게 하기 위해 ```VStack```을 사용합니다.
1. 각 뷰의 사이즈는 명확히 수치를 입력해야 합니다. 여기서 ```Spacer```는 남은 여백을 채워 줍니다. 사실 상 이 코드는, ```Spacer```를 ```VStack```의 가장 위로 지정하는 것입니다.
1. 기초적인 SwiftUI 형태인 ```Rectangle```을 사용합니다. 이름 그대로 사각형 형태이며 ```frame(_:, _:)```함수를 통해 배치 하고, fill()함수를 통하여 채울 색을 지정합니다. ```frame(_:, _:)```의 전달인자는 폭(width)과 높이(height)로 상수(constant)로 설정하며 15가 곱해져 각 달의 총 강우량을 나타낼 것입니다. 참고로, 1인치(inch)의 강우량 수치는 20 포인트(points) 폭, 15 포인트 높이로 표시 됩니다. 예를 들어, 어떤 달에 7인치의 비가 왔다면 강우량 바는 20 포인트의 폭과 105 포인트 높이로 표시됩니다.
1. 각 바에 해당 월을 표시해 주기 위해(라벨) 스택의 가장 마지막 부분에 ```text```뷰를 사용하며 축약형으로(i.e. January -> Jan) 표시하기 위해 ```.footnote```를 ```font()```에 ```frame(_:)```에는 고정(static)값 높이를 지정합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/initial-bars.png" width="231">
</p>

## 세부사항 추가하기, Adding a drop more detail
여기에서는 SwiftUI가 제공하는 기능적 이점들을 알아보며 어떻게 하면 좋은 막대 차트를 만들수 있는지 살펴 보겠습니다. 외부 ```HStack```은 이 차트에서 동등하게 각 바가 차지하는 공간을 나눕니다. 이러한 기능은 코드를 읽기 쉽게(readablity) 해줍니다. 각 바의 높이는 일년동안의 강우량 총량을 보여줍니다.

그러나, 이 차트는 확실하고 정확하게 강우량 총량을 나타내지는 않습니다. 아래의 코드를 ```body```의 ```Spacer``` 아랫 부분에 추가함으로서 불확실함, 불명확함을 해소할 수 있습니다. 

```swift
Text("\(self.sumPrecipitation(month).stringToOneDecimal)")
  .font(.footnote)
  .rotationEffect(.degrees(-90))
  .offset(y: 35)
  .zIndex(1)
```

각 막대에 텍스트뷰(text view)가 추가되었습니다. 이로서 소수 첫번째 자리까지 반올림(rounded to one decimal) 된 한달 총 강우량을 보여줄 수 있게 되었습니다. 사용된 함수는 ```Double```에 확장된 함수(extension method)이며 **DoubleExtension.swift**에서 찾아볼 수 있습니다.

텍스트뷰의 폰트는 x축의 월 라벨(month label)과 동일하며 텍스트는 반시계 방향으로 90도 회전되어 수직막대와 평행한 방향으로 보여집니다. 마지막으로 텍스트뷰는 막대의 안쪽에 위치하도 아래방향으로 35 포인트 떨어지게(offset) 설정했습니다.

SwiftUI는 뷰를 읽는 순서대로 렌더링 합니다. 이 말은, 강우량 수치는 각 막대와 같은 공간을 점유하기에, 그리고 먼저 생성 되었기에 각 바의 아래에 위치하게 된다는 의미입니다.

```zIndex``` 프로퍼티는 디폴트값 0이 아닌, 1이 설정된 이유는 SwiftUI가 ```Text```를 다른 뷰(views)의 최상위에 그리게 하게 위함입니다. 결과적으로, 강수량 수치는 ```zIndex```프로퍼티를 1로 설정함에 의해 각 막대의 위에 위치하게 됩니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/bar-chart-label-231x500.png" width="231">
</p>

빌드와 런을 통해 새로 추가한 text view가 잘 보여지는지 확인해 보십시오. 그 다음, Cherokee, NC 관측소로 이동하여 강수량 탭(precipitation tab)으로 이동하면 재미있는 버그(bug)가 발견될 것입니다. 2018년 7월의 강우량은 아주 소량이기 때문에 막대가 짧게 표시 되며 텍스트(강우량 수치)가 제대로 표시되지 않습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/precip-bar-error-231x500.png" width="231">
</p>

이 버그를 수정하기 위해, offset을 확인하는 코드를 넣어 특정 조건에서는 offset 프로퍼티가 다르게 설정하도록 합니다.

```swift
.offset(y: self.sumPrecipitation(month) < 2.4 ? 0 : 35)
```
만약 한 달 강우량 총량이 2.4인치보다 낮을 경우(막대의 높이로는 36포인트)에는 강우량 수치 텍스트가 바위 상단에 위치하도록 합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/corrected-cherokee-precip.png" width="231">
</p>

리스트를 (세로)막대-차트로 만들었습니다. 이를 통해 유저는 모든 원형(원시) 데이터를 각 달의 강우량의 차이를 선명하게 시각화된 차트로 볼수 있게 되었습니다.

이제 강우량 차트를 완성했으니 다음으로 강설량 데이터를 이용해 가로막대-차트(horizontal bar chart) 만들어 보겠습니다.

## 가로막대-차트 만들기, Building a Horizontal Bar Chart



## 끝으로... , Where to Go From Here

스위프트 개선 프로젝트(Swift Evolution process)에 대해서는 알아보고 싶은신 분은 아래의 링크를 참고 하십시오.
* []() 이 문서는 Swift 업데이트마다 간단한 변경사항에 대한 요약을 제공합니다.

> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭]()하십시오.
