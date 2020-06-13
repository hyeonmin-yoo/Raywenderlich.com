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

## 왜 차트를 사용하는가?
[Why Use a Chart?](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-001)

소수의 몇 가지 데이터를 살펴보며 정보를 팔악할 수는 있지만, 끝 없이 긴 숫자를 보는 것은 데이터에서 통찰을 얻기 위한 좋은 방법은 아닙니다. 수 많은 숫자를 보며 어떤 달이 얼마나 따뜻했는지, 어떤 달이 가장 건조했는지를 파악하는 것은 쉽지 않습니다.

대부분의 사람들은 정보가 그래픽으로 표시될 때 더 쉽게 그 정보에 대해서 파악합니다. 차트는 그래픽으로 표현하여 보는 사람에게 정보를 제공해 줍니다.

### 차트 vs. 그래프
[Charts vs. Graphs](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-002)

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/hypercycloid.png" width="394" height="363">
</p>

차트는 이야기를 해야합니다. 보는 사람으로이 해당 테이터를 이해하기 쉽고, 해석하기 쉽게 해야합니다. 즉, 모든 차트는 그래프이지만, 모든 그래프가 차트는 아닙니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/Screenshot-2019-11-10-18.23.29.png" class="center" width="483">
</p>

## 시작하며...
[Getting Started](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-003)

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

## 차트를 만들기 위한 재조정
[Refactoring for Charts](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-004)

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

## SwiftUI 막대(bar) 올리기
[Raising the SwiftUI Bar](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-005)

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

## 세부사항 추가하기
[Adding a drop more detail](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-006)

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
만약 한 달 강우량 총량이 2.4인치보다 낮을 경우(막대의 높이로는 36포인트)에는 강우량 수치 텍스트가 막대의 상단에 위치하도록 합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/corrected-cherokee-precip.png" width="231">
</p>

리스트를 (세로)막대-차트로 만들었습니다. 이를 통해 유저는 모든 원형(원시) 데이터를 각 달의 강우량의 차이를 선명하게 시각화된 차트로 볼수 있게 되었습니다.

이제 강우량 차트를 완성했으니 다음으로 강설량 데이터를 이용해 가로막대-차트(horizontal bar chart) 만들어 보겠습니다.

## 가로막대-차트 만들기
[Building a Horizontal Bar Chart](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-007)

The Smoky Mountains에는 미국 동부에서 가장 고도가 높은 몇 곳이 있습니다. 그러나 이 높은 고도를 제외하면 예상보다 강우량이 적습니다.

눈이 부족하다는 것은 강우량 차트와 같이 월별 그룹화를 의미하며, 연초와 연말에 탁 튀어 오르고 연중에는 아무것도 표시되지 않을 것입니다. 그러므로 강우량 그래프는 강우량 기록이 있는 날만을 보여주는 수평막대-차트로 그려질 것입니다.

Xcode의 **Charts**그룹에서 우클릭하여 **New File**을 선택합니다. 다시 **SwiftUI VIew**를 선택하고 **Next**를 클릭합니다. 새로운 파일의 이름은 **SnowfallChart**로 합니다. 파일이 **Charts**그룹에 위치하는지 확인하고 **Create**를 클릭하여 만들어진 파일을 열겠습니다.

아래의 코드를 struct의 위에 추가하여 강우량 데이터를 이 뷰에 전달합니다.

```swift
var measurements: [DayInfo]
```

가장 빈번한 강우일과 가장 큰 강우량 합계를 가진 Mount LeConte 관측소 데이터를 사용해 프리뷰를 아래와 같이 바꾸겠습니다.

```swift
SnowfallChart(measurements: WeatherInformation()!.stations[2].measurements)
```

다음으로, ```body```를 아래와 같이 바꾸겠습니다.

```swift
// 1
List(measurements.filter { $0.snowfall > 0.0 }) { measurement in
  HStack {
    // 2
    Text("\(measurement.dateString)")
      .frame(width: 100, alignment: .trailing)
    // 3
    Rectangle()
      .fill(Color.blue)
      .frame(width: CGFloat(measurement.snowfall * 10.0), height: 5.0)
    // 4
    Spacer()
    Text("\(measurement.snowfall.stringToOneDecimal)\"")
  }
}
```
위 코드의 분석은 아래와 같습니다.

1. ```List``` 를 생성하여 각 강설량 측정값을 위한 시작점을 만듭니다.
1. 각 행(row)은 눈이 내린 날짜와 함께 시작합니다. 자동적으로, 텍스트 뷰(```Text``` view)는 포함된 텍스트 크기에 맞게 크기가 조절되며 너비가 다른 행은 남겨둡니다. 특정한 상수값으로 너비를 지정하면 각 행에서 막대의 수평 위치(horizontal position) 위치는 같은 곳으로 적용됩니다. 정렬은 ```.trailing```으로 지정하며 강설량 막대 왼쪽에 표시되도록 합니다.
1. 막대는 파란색 사각형으로 만들어 집니다. 막대의 높이는 상수로 지정하며 너비(좌우의 길이)는 강우량 수치를 입력합니다. 아이폰의 가로 길이는 세로 길이보다 짧기 때문에 한 인치를 나타내기 위해서 이전 수직막대 차트보다 더 적은 포인트를 적용합니다.
1. 가로막대 코드 뒤에 여백을 채워주는 ```Spacer()```채워준 후, 인치 단위로 표현되는 강설량 수치를 ```Text```뷰에 통해 보여줍니다. 소수점 이하 수치의 데이터는 소수점 첫번 째 자리까지 보여주고, 정확히 1 인치인 경우는 1.0과 같이 표기 되도록 하겠습니다.

**SnowfallTab.swift**파일로 돌아가서 ```List```와 ```body``` 안의 클로져(closure)의 내부를 아래와 같이 바꾸겠습니다.

```swift
SnowfallChart(measurements: station.measurements)
```

가로막대 차트는 아래와 같이 보여질 것입니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/initial-snowfall-chart.png" width="231">
</p>

## 눈금 추가하기
[Adding Grid Lines](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-008)

강설량의 수치는 크게 변동하므로, 눈금(grid)을 추가해주면 차드를 보다 읽기 쉽게 할수 있습니다. 격자는 수직선으로 그어지고 차트나 그래프의 위에 일정하게 고정된 형태로 위치됩니다. 이 눈금으로 인해 차트를 보는 사람은 보다 쉽게 막대의 길이를 측정할 수 있게됩니다.

> 원문에서는 grid라는 단어를 사용하고 일반적으로 한국어로는 '격자'라고 해석합니다만, '눈금'이라는 표현이 더 적합한 듯 하여 이하에서는 '눈금'으로 번역합니다.

첫번째로, **SnowfallChart**에서 ```Rectangle()```코드를 아래와 같이 바꿔줍니다.

```swift
ZStack {
  Rectangle()
    .fill(Color.blue)
    .frame(width: CGFloat(measurement.snowfall * 10.0), height: 5.0)
}
```
```ZStack```은 다수의 하위뷰(child views)를 같은 공간에 덮어씌웁니다(overlay). 이 경우에는 가로막대와 눈금이 겹쳐지게 됩니다. 눈금은 1인치 마다 그리고 가장 큰 수치가 16인치이므로 1 ~ 16까지 그려지게 될 것입니다.

아래의 코드를 ```Rectangle```안의 ```ZStack```뒤에 추가합니다.

```swift
ForEach(0..<17) { mark in
  Rectangle()
    .fill(Color.gray)
    .offset(x: CGFloat(mark) * 10.0)
    .frame(width: 1.0)
    .zIndex(1)
}
```

```offset(x:y:)```는 눈금을 오른쪽으로 지정된 상수만큼 이동 시킵니다. 눈금의 너비는 ```.frame(width:)``` 1로 설정되도록 합니다. ```zIndex```를 다시금 1로 설정하여 레이어의 최상위에 위치하도록 합니다.

여기서 주목해야 할 점은 높이(height)를 지정하지 않았다는 것입니다. 이런한 경우에 높이는 기본적으로 상위뷰의 높이 많큼 확장되게 됩니다. 지금까지의 작업 내용을 빌드-런 해보면 뭔가 조금 잘못되었다는 것을 알수 있습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/snowfall-grid-aligned-231x500.png" width="231">
</p>

눈금이 언제나 일정하게 표시되지 않았습니다. ```ZStack```은 자동적으로 하위뷰를 중앙(center) 정렬하기 때문에, 명시적으로 어떻게 하위뷰를 정렬할 것인지 아래와 같이 변경해야 합니다. ```ZStack```부분을 아래와 같이 고치십시오.

```swift
ZStack(alignment: .leading) {
```

이제 눈금과 가로막대로 의도했던대로 보여질 것입니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/initial-snowfall-chart.png" width="231">
</p>

이렇게 수 많은 눈금이 그어질 때, 일정 간격마다 시각적인 단서(visual cue)를 표시한다면 가독성은 더욱 향상 될 것입니다. ```.fill()```을 아래와 같이 바꾸겠습니다.

```swift
.fill(mark % 5 == 0 ? Color.black : Color.gray)
```

위 코드는 삼항연산자(ternary operator)을 통해 사용하여 매 5번째 눈금을 검은색으로 지정합니다. 

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/snowfall-red-mark-1-231x500.png" width="231">
</p>

여기까지 여러분은 두 개의 기본적인 차트를 성공적으로 만들었습니다. 이제 온도 데이터를 표시할 적외선 열지도(heat map)를 만들어 보겠습니다.

## 열지도 만들기
[Creating a Heat Map](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-009)

먼저 TemperatureChart라는 이름으로 새로운 SwiftUI 뷰를 **Charts** 그룹에 만들겠습니다. **TemperatureChart.swift**을 열고 ```struct``` 위에 측정값 데이터 변수를 만들겠습니다.

```swift
var measurements: [DayInfo]
```

preview에 아래의 같이 데이터를 제공 하겠습니다.

```swift
TemperatureChart(measurements: WeatherInformation()!.stations[1].measurements)
```

이번 차트는 한 관측소의 1년 동안의 가장 높은 온도에서 가장 낮은 온도까지를 시각화하여 보여줘야 합니다. 이를 달성하기 위해 몇 가지 보조 함수(helper functions)를 사용해야 하면 아래와 같은 함수를 ```measurements``` 아래에 작성하도록 하겠습니다.

```swift
func degreeHeight(_ height: CGFloat, range: Int) -> CGFloat {
  height / CGFloat(range)
}

func dayWidth(_ width: CGFloat, count: Int) -> CGFloat {
  width / CGFloat(count)
}
````

시행 착오에 의한 정해진 총량을 사용하는 대신에, 이 차트는 위 함수를 통해 스스로 뷰에 맞게 조절됩니다. 첫 번째 함수는 온도 1도 단위당 수직 포인트를, 두 번째 함수는  하루 단위를 이용해 수평적으로 계산합니다. 이 두 함수 모두 치수의 크기(dimension)를 각 요소의 숫자로 나눕합니다. 결과값은 ```Float```형태의 숫자입니다.

위의 함수를 통해, 각 포인트의 위치가 결정됩니다. 아래의 함수를 위 함수의 아래에 추가하십시오.

```swift
func dayOffset(_ date: Date, dWidth: CGFloat) -> CGFloat {
  CGFloat(Calendar.current.ordinality(of: .day, in: .year, for: date)!) * dWidth
}

func tempOffset(_ temperature: Double, degreeHeight: CGFloat) -> CGFloat {
  CGFloat(temperature + 10) * degreeHeight
}
```

```dayOffset(_:dWidth:)```은 넘겨진 날짜 통해 해당 연도의 날짜를 계산하고 ```dWidth``` 전달인자를 통해 곱합니다. 이로서 수평적 위치를 뷰에 정하게 됩니다.

```tempOffset(_:degreeHeight:)``` 함수도 주어진 온도 전달인자를 통해 비슷한 계산을 합니다. 온도의 범위가 -10에서 시작되었기 때문에, 곱셈 전에 다시 10을 더합니다.

```body```는 아래와 같이 바꿉니다.

```swift
// 1
GeometryReader { reader in
  ForEach(self.measurements) { measurement in
    // 2
    Path { p in
      // 3
      let dWidth = self.dayWidth(reader.size.width, count: 365)
      let dHeight = self.degreeHeight(reader.size.height, range: 110)
      // 4
      let dOffset = self.dayOffset(measurement.date, dWidth: dWidth)
      let lowOffset = self.tempOffset(measurement.low, degreeHeight: dHeight)
      let highOffset = self.tempOffset(measurement.high, degreeHeight: dHeight)
      // 5
      p.move(to: CGPoint(x: dOffset, y: reader.size.height - lowOffset))
      p.addLine(to: CGPoint(x: dOffset, y: reader.size.height - highOffset))
      // 6
    }.stroke()
  }
}
```

코드가 많지만, 이 함수는 필요한 계산을 단순화합니다. 아래를 보면 코드가 어떻게 작동하는지 이해 할 수 있습니다.

1. ```GeometryReader```를 생성함으로서, 차트가 감싸집니다. ```GeometryReader```는 뷰가 가지고 있는 컨텐츠를 채우기 위해 스스로 확장됩니다.
이전 차트에서는 수치를 정확하게 표현하기 위해 고정된 크기의 바를 생성했지만, 이번 차트에서는 정확한 값을 계산하기 위해서 위의 함수(```dayOffset(_:dWidth:)``` ```tempOffset(_:degreeHeight:)```)를 사용합니다.
1. ```Path```를 2차원 형상을 만들수 있습니다. 여기서는 하루 하루의 최저-최고 온도를 수직선 형태로 만듭니다. 또한, ```Path```는 SwiftUI에서 특수한 기능을 제공합니다. ```Path``` 영역 안에서 변수를 정의하는 것이 가능하며 이는 포인트의 계산을 쉽게 합니다.
1. 두 함수를 이용해 ```GeometryReader```에서 사용되는 날짜의 온도 포인트와 날짜가 포인트를 계산합니다. 온도 범위는 화씨(Fahrenheit) 110은 -10에서 100도까지를 표현할 수 있으며 이는 해당 연도에 모든 온도 데이터를 표현할 수 있는 범위입니다.
1. 이미 정의된 함수를 통해 각 날짜별 최저-최고 온도의 수직 포인트를 계산합니다.
1. ```Path```의 포인터를 최저 온도 포인트로 이동시켜 최고 온도 포인트까지 선을 긋습니다. 이 수직뷰의 좌표는 뷰의 가장 위에서 시작하여 아래쪽 방향으로 증가합니다. 반대 방향으로 선을 그을 때는, ```reader.size.height```에서 값을 뺌으로서 원하는 위치를 계산합니다.
1. ```stroke()```를 통해 SwiftUI에 선을 만들도록(render)를 합니다. 색(Color)은 기본 색상이 기본값으로(current system color) 설정됩니다.

새로만든 뷰를 표시하기 위해 **TemperatureTab.swift** 파일을 열어 ```body```를 아래와 같이 바꿉니다.

```swift
VStack {
  Text("Temperatures for 2018")
  TemperatureChart(measurements: station.measurements)
}.padding()
```

앱을 빌드-런 하고 관측소 하나를 선택합니다. 그리고 온도 탭(temperature tab)을 터치하여 열지도 맵을 살펴보겠습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/initial-temp-chart.png" width="231">
</p>

이 열지도 차트는 온도 변화를 꽤 잘 보여주고 있습니다만 조금 지루합니다. 다음으로 색을 지정하여 온도 변화를 좀 더 시각적으로 표현해 보겠습니다.

## 열지도에 색 추가하기
[Adding Heat Map Color](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-010)

열지도는 컬러를 사용하여 시각적으로 값을 나타냅니다. 날씨 관련 지도는 온도를 나타낼 때 주로 보라색-연한 파란색을 이용하여 낮은 온도를, 그리고 노랑색-보라색-붉은색으로 높은 온도를 표현합니다. 이러한 온도변화에 따른 색조합은 복잡한 수학적 계산을 필요로 하지만 여기서는 단순화하여 나타내겠습니다.

SwiftUI에서는 이러한 온도 변화에 그레이디언트(gradient)를 사용합니다. 선형(linear) 그레이디언트는 두가지 이상의 색변화를 하나의 축(single axis)을 따라서 매끄럽게 만듭니다. 아래의 코드를 **TemperatureChart.swift**의 ```measurements```아래, 헬퍼 함수 사이에 추가하도록 하겠습니다.

```swift
let tempGradient = Gradient(colors: [
  .purple,
  Color(red: 0, green: 0, blue: 139.0/255.0),
  .blue,
  Color(red: 30.0/255.0, green: 144.0/255.0, blue: 1.0),
  Color(red: 0, green: 191/255.0, blue: 1.0),
  Color(red: 135.0/255.0, green: 206.0/255.0, blue: 250.0/255.0),
  .green,
  .yellow,
  .orange,
  Color(red: 1.0, green: 140.0/255.0, blue: 0.0),
  .red,
  Color(red: 139.0/255.0, green: 0.0, blue: 0.0)
])
```

위 코드는 12가지 색상으로 구성된 그레이디언트를 110도까지의 온도 범위를 10도 단위로 나타냅니다. 시작은 -10도는 보라색으로 100도는 검은색을 사용하겠습니다.

```body```부분에서 ```stroke()```를 아래와 같이 바꾸겠습니다.

```swift
.stroke(LinearGradient(
  gradient: self.tempGradient,
  startPoint: UnitPoint(x: 0.0, y: 1.0),
  endPoint: UnitPoint(x: 0.0, y: 0.0)))
```

검은색으로 단조롭게 표현되던 색을 위에서 정의한 선형 그레이디언트를 사용하였습니다. ```startPoint```와 ```startPoint```매개변수는 거의 마법과 같은 기능을 합니다.

두 매개변수 모두 ```UnitPoints```를 사용하고 ```UnitPoints```는 뷰의 가장자리인 0.0과 1.0 사이 사이에서 온도변화에 따른 색을 표현하게 됩니다. 0.0은 시작 지점(origin)이고 뷰 기준으로 왼쪽-상단입니다.

시작 지점으로 뷰의 왼쪽-아래를, 끝나는 지점은 왼쪽-상단을 지정했습니다. 이 그레이디언트는 선형 그레이디언트이기 때문에 색 변화는 수직적으로만 나타나며 수평적으로는 동일한 색상만 보여집니다.

런-빌드하여 결과를 확인하겠습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/temp-color.png" width="231">
</p>

## 격자-눈금선(Grid lines)과 라벨 추가하기
[Adding Grid Lines and Labels](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-011)

이제 남은 일은 막대차트에서와 같이 유저로 하여금 차트를 조금 더 읽기 쉽도록 격자-눈금선을 추가하는 것입니다.**TemperatureChart.swift**에 아래의 헬퍼 함수를 추가하십시오.

```swift
func tempLabelOffset(_ line: Int, height: CGFloat) -> CGFloat {
  height - self.tempOffset(
    Double(line * 10),
    degreeHeight: self.degreeHeight(height, range: 110))
}
```

위 코드는 격자-눈금선을 10도 단위의 블럭(blocks)으로 나누고 리턴값으로는 뷰의 높이에 따라 10으로 나뉘어진 시작 온도값을 나타내는 ```integer```를 돌려줍니다.
이 함수는 적절한 ```offset```을 계산합니다.

격자-눈금선과 라벨을 그리기 위해 아래의 코드를 ```body```의 ```forEach```의 닫힌 브레이스(closing bracket) 아래의 추가하겠습니다.

```swift
// 1
ForEach(-1..<11) { line in
  // 2
  Group {
    Path { path in
      // 3
      let y = self.tempLabelOffset(line, height: reader.size.height)
      path.move(to: CGPoint(x: 0, y: y))
      path.addLine(to: CGPoint(x: reader.size.width, y: y))
      // 4
    }.stroke(line == 0 ? Color.black : Color.gray)
    // 5
    if line >= 0 {
      Text("\(line * 10)°")
        .offset(x: 10, y: self.tempLabelOffset(line, height: reader.size.height))
    }
  }
}
```
코드 분석은 아래와 같습니다.

1. 반복문은 화씨 -10도에서 100도까지 나타내기 위해 -1에서 10까지 반복됩니다.
1. ```Group```뷰는 SwiftUI에서 풀(glue)과 같은 역할을 합니다. 하위 뷰를 결합하지만 직접적으로 렌더링하지는 않습니다. 여기에서는 ```Path```와 ```Text()```가 그 역할을 합니다.
1. 조금 전에 추가한 함수 ```tempLabelOffset(_:_:)```을 사용하여 그려질 (격자-눈금선 중에) 수평라인의 위치를 계산하고 왼쪽에서 오른쪽으로 그립니다.
1. 대부분의 수평라인은 회색(gray)으로, 0도를 나타내는 선은 검은색으로 그려집니다.
1. 첫 번째를 제외한 모든 라인에는 라벨이 붙여집니다. 더 이상 ```Path```영역이 아니기 때문에 라벨이 추가될 위치, 즉 각 수평라인의 위치를 다시 계산헤야 합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/11/temp-grid-lines.png" width="231">
</p>

## 끝으로...
[Where to Go From Here](https://www.raywenderlich.com/6398124-swiftui-tutorial-for-ios-creating-charts#toc-anchor-012)

스위프트 개선 프로젝트(Swift Evolution process)에 대해서는 알아보고 싶은신 분은 아래의 링크를 참고 하십시오.
* []() 이 문서는 Swift 업데이트마다 간단한 변경사항에 대한 요약을 제공합니다.

> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭]()하십시오.
