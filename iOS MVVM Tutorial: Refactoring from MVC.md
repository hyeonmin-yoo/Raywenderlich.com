- *This article/tutoral is **permitted translation in Korean language by [raywenderlich.com](https://www.raywenderlich.com/)***

- *이 글/튜토리얼은 raywenderlich.com의 **허가를 받고 번역**하였습니다.*

- *전문 번역가의 번역이 아니며 **다소의 오역/의역 혹은 과감한 생략**이 있을 수 있음을 알려드립니다.*

-----

# iOS를 위한 MVVM 튜토리얼: MVC에서 리팩토링 하기

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

위의 **Download Materials** 링크를 통해 프로젝트를 다운로드 받아 begin 프로젝트를 실행합니다.

이 앱은 weatherbit.io에서 최신 날씨 정보를 가져와 요약된 현재 날씨를 보여줍니다.

Weatherbit API를 사용하기 위해서는 무료 API 키(key)를 발급받을 필요가 있습니다. 이 앱은 여러분 소유의 Weatherbit API 키를 추가하지 전까지 제대로 동작하지 않으므로 [https://www.weatherbit.io/account/create](https://www.weatherbit.io/account/create)로 이동해 key 등록하기 바랍니다.

API 키 발급 받았다면 Xcode로 돌아가서 **Services** 그룹 아래, WeatherbitService.swift 파일을 열겠습니다. 그리고 apiKey 변수에 발급받은 API 키를 지정합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2020/03/Screen-Shot-2020-04-01-at-6.44.14-PM.png" width="750">
</p>

빌드-런 하면 오늘 날짜의 McGaheysville, VA 지역의 날씨를 확인할 수 있습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/Simulator-Screen-Shot-iPhone-8-2019-12-21-at-22.36.20.png" width="281">
</p>

## MVVM의 기능과 의무
[Introducing MVVM Roles and Responsibilities](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc#toc-anchor-002)

본격적인 리팩토링에 앞서, view model과 view controller가 MVVM 패턴에 있어 어떤 기능을 하는지에 대한 이해는 필수적입니다.

view controller는 뷰를 교체하고 뷰의 인풋(inputs)을 view model에 전달할 의무를 가집니다. 결과적으로, 다른 모든 코드 혹은 로직은 view model로 이동 될 것입니다.

그에 반해, view model은 아래와 같은 의무를 가집니다.
* **Model inputs**: view의 inputs를 취해 model을 업데이트 합니다.
* **Model outputs**: Model의 outputs를 view controller에 전달합니다.
* **Formatting**: view controller에서 표시할 model 데이터를 형식화 합니다.

## MVC 구조에 익숙해 지기
[Becoming Familiar With the Existing App Structure](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc#toc-anchor-003)

> 만약, MVC에 익숙하고 바로 리팩토링하길 원한다면 [Data Binding Using Box](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-005) 섹션으로 이동하십시오.

MVC 설계에 익숙해 지는 시간을 갖겠습니다. 먼저, 아래와 같이 프로젝트 네비게이터(Project navigator)를 열겠습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2020/03/Screen-Shot-2020-04-01-at-7.24.52-PM.png" width="272">
</p>

**Controllers** 그룹에서 **WeatherViewController.swift** 파일을 볼 수 있습니다. 이 파일에서 여러분은 model과 service 관련 코드를 제거할 것입니다. 

**Models** 그룹에서 두 가지 모델 오브젝트인 ```WeatherbitData```와 ```Location```이 있습니다. ```WeatherbitData```는 struct 구조이고 Weatherbit API에서 보내진 데이터를 담습니다. ```Location```은 단순화된 struct 구조로 Apple의 ```CLLocation``` 서비스에서 보내진 데이터를 담습니다.

**Services** 그룹은 각 파일명이 의미하는 바와 같이, **WeatherbitService.swift**는 Weatherbit API에서 날씨 데이터를 가져오는 기능을, **LocationGeocoder.swift**는 string을 ```Location```으로 변환하는 기능을 구현 합니다.

**Storyboards** 그룹에는 LaunchScreen과 Weather 스토리보드가 포함되어 있습니다.

**Utilities** 그룹과 **View Models**에는 아직 아무 파일도 없지만, 여러분이 리팩터하는 동안 몇몇 파일을 만들 것 입니다.

### WeatherViewController




## 끝으로...
[Where to Go From Here](https://www.raywenderlich.com/6733535-ios-mvvm-tutorial-refactoring-from-mvc#toc-anchor-011)

글 상단의 **Download Materials** 버튼을 통해 완성된 프로젝트를 다운로드 할 수 있습니다.


> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭](https://forums.raywenderlich.com/t/ios-mvvm-tutorial-refactoring-from-mvc-raywenderlich-com/100270/24)하십시오.
