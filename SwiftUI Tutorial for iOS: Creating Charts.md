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



## 끝으로... , Where to Go From Here

아직 튜도리얼 자료를 다운받지 않으신 분들은 이글의 상단에서 다운로드 버튼을 통해 더 많은 Swift 5.2 개선/변경 사항을 확인 할 수 있습니다.

Swift 5.2 업데이트가 Major update는 아니지만 변경되거나 추가된 사항이 많습니다. 많은 개발자들이 개선사항들을 통해 더 나은 진단, 테스트, 에러 메세지를 경험할수 있습니다. 특히, 머신러닝 프로젝트에 관계된 개발자들은 새로 추가된 타입과 Key-Path 기능을 통해 원활한 작업을 할수 있을 것입니다. 또한, 개선된 커스텀 콜렉션(Custom Collections)은 개발자들이 기본 서브스크립트 타입(Default Subscript Types)을 추가하는데 도움을 줄 것입니다.

스위프트 개선 프로젝트(Swift Evolution process)에 대해서는 알아보고 싶은신 분은 아래의 링크를 참고 하십시오.
* [GitHub change log for Swift](https://github.com/apple/swift/blob/master/CHANGELOG.md) 이 문서는 Swift 업데이트마다 간단한 변경사항에 대한 요약을 제공합니다.
* [GitHub listing of Swift Evolution proposals](https://github.com/apple/swift-evolution/tree/master/proposals) 이 문서는 GitHub 페이지를 통해 개선제안사항(Proposal)을 볼 수 있습니다. 이 개선제안사항(들)을 통해 (간단한 요약만으로는 알수 없는) 각 변경/개선 사항에 대한 자세한 이유와 개념을 알수 있습니다.
* [Swift.org bugs and issues dashboard](https://bugs.swift.org/secure/Dashboard.jspa) 이 사이트를 통해서 알려진 Swift 버그와 이슈에 대한 상세한 묘사, 토론, 활동을 볼 수 있습니다.

> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭](https://forums.raywenderlich.com/t/what-s-new-in-swift-5-2-raywenderlich-com/105173)하십시오.
