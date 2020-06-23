- *This article/tutoral is **permitted translation in Korean language by [raywenderlich.com](https://www.raywenderlich.com/)***

- *이 글/튜토리얼은 raywenderlich.com의 **허가를 받고 번역**하였습니다.*

- *전문 번역가의 번역이 아니며 **다소의 오역/의역 혹은 과감한 생략**이 있을 수 있음을 알려드립니다.*

-----

# iOS용 MVVM 튜토리얼: MVC에서 리팩토링 하기

[iOS MVVM Tutorial: Refactoring from MVC](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc) by [Chuck Krutsinger](https://www.raywenderlich.com/u/ckrutsinger)

이 튜토리얼을 통해, 여러분은 MVC 패턴의 앱을 어떻게 MVVM으로 변환하는지, 그리고 MVVM을 도입함으로서 얻을수 있는 이점이 무엇인지 배울수 있습니다.

* **Version**: Swift 5, iOS 13, Xcode 11
* **Level**: Intermediate
* [**Download Materials**](https://koenig-media.raywenderlich.com/uploads/2020/03/MVVM-From-MVC-1.zip)


**Model-View-ViewModel (MVVM)** 디자인 패턴(design pattern)은 최근 몇년동안 iOS 개발자 커뮤니티에서 주목을 받고 있습니다. MVVM 패턴은 **view model** 이라는 새로운 개념을 포함하며 view cotroller와 동반(companion)하는 객체입니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/MVVM-Diagram.png" height="500">
</p>

위에서 보는것과 같이, MVVM 패턴은 세 층(three layers)으로 구성됩니다.

* **Model**: 앱이 동작하도록 사용되는 데이터입니다.
* **View**: 눈에 보여질수 있는 유저 인터페이스(user interface)입니다. iOS앱에서 View는 view controller와 떼어서 생각할 수 없는 개념입니다.
* **ViewModel**: 뷰-인풋(view inputs)으로부터 Model을 업데이트하고 model의 아웃풋(outputs)으로 View를 업데이트 하는 역할을 담당합니다.

MVVM 패턴은 iOS에서 MVC(Model-View-Controller) 패턴에 비해 몇 가지 이점을 제공합니다.

* **복잡성 감소(Reduced complexsity)**: MVVM 패턴하에서는 많은 코드(business logic)가 view controller에서 다른 파일로 이동하기 때문에 view controller가 보다 간결해 집니다.
* **우수한 표현력(Expressive)**: view model은 view의 비지니스 로직을 더 잘 표현합니다.
* **용이한 테스트(Testability)**: view model은 view controller에 비해 테스트하기 더 쉽습니다. view의 구현 전에 비지니스 로직을 테스트할 수 있습니다.

이 튜토리얼에서 여러분은 날씨 앱을 MVC 패턴에서 MVVM 패턴으로 변환(refactor)합니다. 모든 날씨, 위치 관련 코드를 view controller에서 view model로 옮기고, 얼마나 쉽게 view model에  통합되었는지 보기 위해 유닛 테스트를 하게됩니다.

이 튜토리얼이 끝나면, 이름으로 위치를 선택하여 해당 지역의 날씨 요약정보를 볼수 있게 됩니다.

## 시작하며...
[Getting Started](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc#toc-anchor-001)







## 끝으로...
[Where to Go From Here](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc#toc-anchor-011)

글 상단의 **Download Materials** 버튼을 통해 완성된 프로젝트를 다운로드 할 수 있습니다.


> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭](https://forums.raywenderlich.com/t/ios-mvvm-tutorial-refactoring-from-mvc-raywenderlich-com/100270/24)하십시오.
