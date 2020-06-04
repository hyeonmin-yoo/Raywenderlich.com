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

강우량 데이터를 바 차트(bar chart)에 추가하는 작업부터 시작하겠습니다.

## Refactoring for Charts

바 차트에서 바는 각 데이터 요소를 표현합니다. 각 바의 길이는 수치적인 값을 나타내며 필요에 따라 수직적으로, 수평적으로 표현됩니다.

프로젝트 네비게이터에서 **Tabs** 그룹안의 **PrecipitationTab.swift** 파일을 여십시오. 55번째 라인에서 SwiftUI ```List()```를 볼수 있습니다. ```List()```는 0부터 11까지 반복하며 해당 연월과 총 강우량을 나타내고 표시합니다. ```List()``` 내부의 ```monthAbbreviationFromInt```, ```sumPrecipitation```함수는 각 달의 이름과 강우량 합계를 계산합니다.

**Chart**그룹에서 


## 끝으로... , Where to Go From Here

아직 튜도리얼 자료를 다운받지 않으신 분들은 이글의 상단에서 다운로드 버튼을 통해 더 많은 Swift 5.2 개선/변경 사항을 확인 할 수 있습니다.

Swift 5.2 업데이트가 Major update는 아니지만 변경되거나 추가된 사항이 많습니다. 많은 개발자들이 개선사항들을 통해 더 나은 진단, 테스트, 에러 메세지를 경험할수 있습니다. 특히, 머신러닝 프로젝트에 관계된 개발자들은 새로 추가된 타입과 Key-Path 기능을 통해 원활한 작업을 할수 있을 것입니다. 또한, 개선된 커스텀 콜렉션(Custom Collections)은 개발자들이 기본 서브스크립트 타입(Default Subscript Types)을 추가하는데 도움을 줄 것입니다.

스위프트 개선 프로젝트(Swift Evolution process)에 대해서는 알아보고 싶은신 분은 아래의 링크를 참고 하십시오.
* [GitHub change log for Swift](https://github.com/apple/swift/blob/master/CHANGELOG.md) 이 문서는 Swift 업데이트마다 간단한 변경사항에 대한 요약을 제공합니다.
* [GitHub listing of Swift Evolution proposals](https://github.com/apple/swift-evolution/tree/master/proposals) 이 문서는 GitHub 페이지를 통해 개선제안사항(Proposal)을 볼 수 있습니다. 이 개선제안사항(들)을 통해 (간단한 요약만으로는 알수 없는) 각 변경/개선 사항에 대한 자세한 이유와 개념을 알수 있습니다.
* [Swift.org bugs and issues dashboard](https://bugs.swift.org/secure/Dashboard.jspa) 이 사이트를 통해서 알려진 Swift 버그와 이슈에 대한 상세한 묘사, 토론, 활동을 볼 수 있습니다.

> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭](https://forums.raywenderlich.com/t/what-s-new-in-swift-5-2-raywenderlich-com/105173)하십시오.
