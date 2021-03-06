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

리팩터링을 하면서 여러분은 주로 ```WeatherViewController```에 집중하게 될 것입니다. ```WeatherViewController```의 private 프로퍼티를 살펴보는 것으로 시작 하겠습니다.

```swift
// 1
private let geocoder = LocationGeocoder()
// 2
private let defaultAddress = "McGaheysville, VA"
// 3
private let dateFormatter: DateFormatter = {
  let dateFormatter = DateFormatter()
  dateFormatter.dateFormat = "EEEE, MMM d"
  return dateFormatter
}()
// 4
private let tempFormatter: NumberFormatter = {
  let tempFormatter = NumberFormatter()
  tempFormatter.numberStyle = .none
  return tempFormatter
}()
```

1. ```geocoder```는 *Washington DC*와 같은 ```string``` input을 취하고 위도(latitude)와 경도(longitude)로 변환하여 날씨 서비스에 보내집니다.
1. ```defaultAddress```에는 기본 주소를 담습니다.
1. ```DateFormatter```는 날짜 형식을 지정합니다.
1. 마지막으로, ```NumberFormatter```는 정수(integer)값 형태로 온도를 표현하도록 합니다.

```viewDidLoad()```를 보겠습니다.

```swift
override func viewDidLoad() {
  geocoder.geocode(addressString: defaultAddress) { [weak self] locations in
    guard 
      let self = self,
      let location = locations.first 
      else { 
        return 
      }
    self.cityLabel.text = location.name
    self.fetchWeatherForLocation(location)
  }
}
```

```viewDidLoad()```는 ```geocoder```를 호출하여 ```defaultAddress```를 ```Location```으로 변환합니다. 콜백(callback)은 ```cityLabel```의 text를 위해 사용 됩니다. 그 후, ```location```은 ```fetchWeatherForLocation(_:)```에 전달되어 날씨 데이터를 취득하게 됩니다.

마지막 부분은 ```WeatherViewController```의 ```fetchWeatherForLocation(_:)```입니다.

```swift
func fetchWeatherForLocation(_ location: Location) {
  //1
  WeatherbitService.weatherDataForLocation(
    latitude: location.latitude,
    longitude: location.longitude) { [weak self] (weatherData, error) in
    //2
    guard 
      let self = self,
      let weatherData = weatherData 
      else { 
        return 
      }
    self.dateLabel.text =
      self.dateFormatter.string(from: weatherData.date)
    self.currentIcon.image = UIImage(named: weatherData.iconName)
    let temp = self.tempFormatter.string(
      from: weatherData.currentTemp as NSNumber) ?? ""
    self.currentSummaryLabel.text =
      "\(weatherData.description) - \(temp)℉"
    self.forecastSummary.text = "\nSummary: \(weatherData.description)"
  }
}
```

위 함수는 두 가지 기능을 합니다.

1. 위도와 경도를 전달하여 WeatherbitService를 호출 합니다.
1. WeatherbitService의 콜백으로 뷰를 업데이트 합니다.

이제, 기존 앱 구조에 대한 충분히 익숙해 졌으므로 리팽토링을 시작 하겠습니다.

## Box를 이용한 데이터 바인딩
[Data Binding Using Box](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-005)

MVVM에서 view model의 outputs을 view에 바인딩해야 합니다. 그런 의미에서 간단한 메카니즘(mechanism)으로 view와 view model의 outputs를 바인딩하는 utility가 필요 합니다. 여기에는 아래와 같은 몇가지 방법이 있습니다.

* **Key-Value Observing or KVO**: key-paths를 이용하여 property를 관찰하고 프로퍼티의 값이 변경된 경우에는 통지(notifications)를 합니다.
* **Functional Reactive Programming or FRP**: 이벤트(events)와 데이터를 연속적(streams)으로 처리하는 체계(paradaigm) 입니다. 새로 출시된 Combine 프레임워크가 바로 FRP에 대한 Apple의 접근법 입니다. RxSwift와 ReactiveSwift 또한 인기있는 FRP 프레임워크 입니다.
* **Delegation**: 값의 변화에 따라 notification을 전달하는 방법으로 델리게이트 함수(delegate methods)를 이용 합니다.
* **Boxing**: 값이 변화할 때 프로퍼티 옵서버(property observers)를 이용하여 notification 합니다.

이 튜토리얼에서 여러분은 boxing으로 binding할 것입니다. 규모가 작은 앱에서는 boxing을 조금 거스터마이징(a custom implementation)하여 구현하는 것으로 충분 합니다.

**Utilities** 그룹 아래에서 Box.swift 파일을 새로 만들어 아래의 코드를 추가 하겠습니다. 

```swift
final class Box<T> {
  //1
  typealias Listener = (T) -> Void
  var listener: Listener?
  //2
  var value: T {
    didSet {
      listener?(value)
    }
  }
  //3
  init(_ value: T) {
    self.value = value
  }
  //4
  func bind(listener: Listener?) {
    self.listener = listener
    listener?(value)
  }
}
```

코드 해석입니다.

1. 각 ```Box```는 해당 값이 변경 되었을 경우에 통지해줄 ```Listener```를 가질수 있습니다.
1. ```Box```는 generic type 값(value)를 가집니다. ```didSet``` 프로퍼티 옵서버는 어떤 변화도 감지하고 ```Listener```에 update된 새 값을 통지 합니다.
1. 이니셜라이저(initializer)는 ```Box``` 초기값을 지정합니다.
1. ```Listener```가 ```Box```에서 ```bind(listener:)```를 호출하면 listener가 되고 즉시 ```Box```의 현재값을 통지 합니다.

## WeatherViewModel 만들기
[Data Binding Using Box](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-005)

view와 view model 사이에서 데이터를 바인딩 하는 메카니즘을 구현했으므로, 이제 실제 view model을 만들 차례입니다. MVVM에서 view controller는 어떤 서비스(services)도 호출하지 않고 어떤 model유형도 조작하지 않습니다. 그 책임은 view model에 배타적으로 부여됩니다.

리팩토링은 geocoder와 Weatherbit service 관련 코드를 ```WeatherViewController```에서 ```WeatherViewModel```로 옮기는 작업부터 시작하겠습니다. 그 후, ```WeatherViewController``` view와 view model 프로퍼티를 바인딩하겠습니다.

먼저, **View Models**그룹에 **WeatherViewModel.swift** 파일을 생성하고 아래의 코드를 추가합니다.

```swift
// 1
import UIKit.UIImage
// 2
public class WeatherViewModel {
}
```

코드 분석입니다.

1. 먼저, ```UIKit.UIImage```을 import 했습니다. 다른 ```UIKit``` 타입은 import 될 필요가 없습니다. 경험에 따르면 ```UIKit```은 절대 import 될 필요가 없습니다.
1. 테스트 때 접근하기 위해 ```WeatherViewModel``` 클래스의 수식어로 ```public```을 지정합니다. 

**WeatherViewController.swift** 파일을 열어 아래의 프로퍼티를 추가합니다.

```swift
private let viewModel = WeatherViewModel()
```

WeatherViewController에 view model을 초기화 했습니다.

다음으로, ```WeatherViewController```의 ```LocationGeocoder``` 로직을 ```WeatherViewModel```로 옮깁니다. 아래의 작업을 마무리 하기전까지는 컴파일(compile) 하지 않겠습니다.

1. 먼저, ```WeatherViewController```에서 ```defaultAddress```를 잘라내기(cut) 해서 ```WeatherViewModel```에 붙여넣기 하겠습니다. 그후, **static** 수식어를 추가해 줍니다.
1. 다음으로, ```WeatherViewController```에서 ```geocoder```를 잘라내기 해서 역시 ```WeatherViewModel```에 붙여 넣습니다.

```WeatherViewModel```에서 아래의 프로퍼티를 추가합니다.

```swift
let locationName = Box("Loading...")
```

위 코드는 앱이 런치(launch)될 때부터 위치 데이터를 가져올 때까지 **"Loading..."** 을 표시해 줍니다.

다음 함수를 ```WeatherViewModel```에 추가합니다.

```swift
func changeLocation(to newLocation: String) {
  locationName.value = "Loading..."
  geocoder.geocode(addressString: newLocation) { [weak self] locations in
    guard let self = self else { return }
    if let location = locations.first {
      self.locationName.value = location.name
      self.fetchWeatherForLocation(location)
      return
    }
  }
}
```

이 코드는 ```geocoder```를 통해 대이터를 가져오기 전에 통해 ```locationName.value```를 **Loading…**으로 변경합니다. ```geocoder```가 검색을 완료하면 ```locationName.value```를 업데이트하고 위치에 대한 날씨 정보를 가져옵니다.

```WeatherViewController.viewDidLoad()```는 아래와 같이 교체합니다.

```swift
override func viewDidLoad() {
  viewModel.locationName.bind { [weak self] locationName in
    self?.cityLabel.text = locationName
  }
}
```

이 코드는 ```cityLabel.text```에 ```viewModel.locationName```를 바인딩합니다.

다음으로,  **WeatherViewController.swift**에서 ```fetchWeatherForLocation(_:)```를 삭제합니다.

아직 위치에 따른 날씨 데이터가 필요하므로 **WeatherViewModel.swift**에서 아래의 리펙터된 ```fetchWeatherForLocation(_:)```를 추가합니다.

```swift
private func fetchWeatherForLocation(_ location: Location) {
  WeatherbitService.weatherDataForLocation(
    latitude: location.latitude, 
    longitude: location.longitude) { [weak self] (weatherData, error) in
      guard 
        let self = self,
        let weatherData = weatherData 
        else { 
          return 
        }
  }
}
```

현재까지 콜백(callback)은 아무런 기능도 하지 않지만 다음 섹션에서 함수를 완성하게 됩니다.

마지막으로, ```WeatherViewModel```에 생성자(initializer)를 추가 합니다.

```swift
init() {
  changeLocation(to: Self.defaultAddress)
}
```

view model은 location에 default address를 지정하는 것으로 시작합니다.

~~Phew!~~ 지금까지 수 많은 리팩토링을 해왔습니다. 여러분은 모든 service와 geocoder 로직을 view controller에서 view model로 이동했습니다. view controller의 코드 얼마나 눈에 띄게 줄어들었는지, 그리고 또 얼마나 단순해 졌는지 보십시오!

앱이 어떻게 동작하는지 보기 위해 ```defaultAddress```를 여러분의 현재 위치로 설정하여 빌드-런 해보길 바랍니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/Simulator-Screen-Shot-iPhone-8-2019-12-21-at-23.27.09.png" height="500">
</p>

여러분의 현재 위치를 기반으로 도시 이름은 제대로 표시되지만 날씨 정보와 날짜는 잘못되어 있습니다. 이 값들은 단지 스토리보드(storyboard)에 임의로 입력된 값을 보여주고 있기 때문입니다. 다음 섹션에서 이 부분을 바로잡도록 하겠습니다.

## MVVM에서 데이터 형식화 하기
[Formatting Data in MVVM](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-007)

MVVM에서 view controller는 view로서의 의무만을 가집니다. view model은 항상 각종 서비스로부터의 데이터터를 view에 나타내기 위한 형식화의 의무를 가집니다.

다음 리팩토링에서는 데이터 형식화(data formatting) 관련 코드를 ```WeatherViewController```에서 ```WeatherViewModel```로 옮기겠습니다. 또한, 위치가 변경되면 날씨 데이터가 업데이트 되도록 나머지 데이터 바인딩을 모두 추가하겠습니다.

데이터 포맷팅을 지정하는 것으로 시작하겠습니다. 먼저, ```WeatherViewController```에서 ```dateFormatter``` 프로퍼티를 잘라내기 하여 ```WeatherViewModel```에 붙여넣기 합니다.

다음으로, ```WeatherViewModel```에 아래의 코드를 ```locationName``` 아래에 추가합니다.

```swift
let date = Box(" ")
```

위 코드는 공백(a blank string)으로 초기화 되지만, Weatherbit API에서 날씨 정보가 도착하면 업데이트 됩니다.

아래의 코드를 ```WeatherViewModel.fetchWeatherForLocation(_:)```안의 API fetch 클로져의 끝 뒤에 위에 추가합니다.

```swift
self.date.value = self.dateFormatter.string(from: weatherData.date)
```

위 코드는 날씨 데이터가 도착할 때 마다 ```date```를 업데이트합니다.

마지막으로, 아래의 코드를 ```WeatherViewController.viewDidLoad()```의 끝에 붙여넣기 합니다.

```swift
viewModel.date.bind { [weak self] date in
  self?.dateLabel.text = date
}
```

빌드-런 합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/Simulator-Screen-Shot-iPhone-8-2019-12-21-at-23.36.28.png" height="500">
</p>

모든 날씨 정보가 이제 ```defaultAddress```에 업데이트 됩니다. 만약, 현재 위치를 사용한 경우 창을보고 데이터가 올바른지 확인하십시오. :] 다음으로 MVVM이 어떻게 앱 기능을 확장할 수 있는지 살펴 보겠습니다.

## MVVM에 기능 추가하기
[Adding Functionality in MVVM](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-008)

지금까지, 기본 위치의 날씨를 확인할 수 있었습니다. 그러나 다른 곳의 날씨를 알고 싶다면 어떻게해야 할까요? 버튼 하나를 추가하여 다른 위치의 날씨를 확인하는 기능을 만들어 보겠습니다. 

화면 위 왼쪽에 로케이션 마크(➤)가 있다는 것, 눈치 채셨나요? 아직 작동하지 않지만, 새로운 위치를 묻는 알림에 연결하여 새로운 위치의 날씨를 가져 오겠습니다.

먼저, **Weather.storyboard**을 열고, 보조창(assistant editor)에는 **WeatherViewController.swift**를 열겠습니다.

다음으로, **Change Location Button**을 콘트롤 드래그(contrl-drag)하여 ```WeatherViewController```의 끝에 ```promptForLocation```이라고 함수명을 지정 하겠습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/assistanteditor.gif" height="409">
</p>

```promptForLocation(_:)```에 아래의 코드를 추가합니다.

```swift
//1
let alert = UIAlertController(
  title: "Choose location",
  message: nil,
  preferredStyle: .alert)
alert.addTextField()
//2
let submitAction = UIAlertAction(
  title: "Submit", 
  style: .default) { [unowned alert, weak self] _ in
    guard let newLocation = alert.textFields?.first?.text else { return }
    self?.viewModel.changeLocation(to: newLocation)
}
alert.addAction(submitAction)
//3
present(alert, animated: true)
```

코드 분석입니다:
1. ```UIAlertController```를 만들고 텍스트-필드를 추가했습니다.
1. ```Submit```하기 위한 액션 버튼(action button)을 추가하였고 이 버튼은 ```viewModel.changeLocation(to:)```에 새로운 위치의 string을 전달합니다.
1. ```alert```를 표시합니다.

빌드-런 합니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/locations.gif" width="231">
</p>

Paris, France나 Paris, Texas등의 위치를 입력합니다. 앱이 어떻게 반응하는지 보기위해 *ggggg*와 같은 무의미한 input을 시도해 봅니다.

잠시 생각해 보면 view controller에 이 새로운 기능을 추가하는데 얼마나 적은 코드만이 필요했는지 알수 있습니다. 뷰 모델을 한 번 호출하면 해당 위치의 날씨 데이터를 업데이트하기 위한 흐름이 촉발됩니다. ~~Smart, right?~~

다음섹션에서는 MVVM에서 어떻게 유닛 테스트(Unit Test)를 하는지 배우게 됩니다.

## MVVM에서 유닛 테스트하기
[Unit Testing With MVVM](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-009)

MVVM의 이점 중 하나는 자동화된 테스트(automated tests)를 쉽게 만들수 있다는 점 입니다.

MVC에서 view controller를 테스트 할 때는 ```UIKit```을 반드시 인스턴트화(instantiate) 해야 했습니다. 그런 다음, 작업을 시작하고 결과를 확인하려면 뷰-계층(view hierarchy)을 철저히 조사해 봐야 했습니다.

MVVM을 사용하면 보다 일반적인(conventional) 테스트를 작성할 수 있습니다. 일부 비동기 이벤트를 기다리는 경우가 있을 수도 있지만 대개의 경우, 작동 및 확인이 쉽습니다.

MVVM이 view model 테스트를 얼마나 용이하게 하는지 확인하기 위해, ```WeatherViewModel```을 만들고 위치(location)를 변경 후, ```locationName``` 바인딩이 의도한 위치로 바뀌었는지 확인하겠습니다.

먼저, **MVVMFromMVCTests**그룹에서 **Unit Test Case Class**파일을 **WeatherViewModelTests**으로 만들겠습니다.

```import XCTest``` 바로 아래에 아래 코드를 추가합니다.

```swift
@testable import Grados
```

다음으로, 아래의 함수를 ```WeatherViewModelTests```에 추가합니다.

```swift
func testChangeLocationUpdatesLocationName() {
  // 1
  let expectation = self.expectation(
    description: "Find location using geocoder")
  // 2
  let viewModel = WeatherViewModel()
  // 3
  viewModel.locationName.bind {
    if $0.caseInsensitiveCompare("Richmond, VA") == .orderedSame {
      expectation.fulfill()
    }
  }
  // 4
  DispatchQueue.main.asyncAfter(deadline: .now() + 2) {
    viewModel.changeLocation(to: "Richmond, VA")
  }
  // 5
  waitForExpectations(timeout: 8, handler: nil)
}
```

위 코드의 설명은 아래와 같습니다.

1. ```locationName``` 바인딩은 비동기입니다. ```expectation```을 사용하여 비동기 이벤트를 기다립니다.
1. 테스트할 ```viewModel```의 인스턴트를 만듭니다.
1. ```locationName```에 바인드하고 값이 일치하는 경우에만 expectation을 실행 합니다. 기본 주소와 같은 위치 이름 값은 무시하십시오. 반드시 예상 결과만 값만이 테스트 기대치를 충족 시킵니다.
1. 위치를 변경하여 테스트를 시작 하십시오. 대기중인 지오코딩(geocoding) 작업이 먼저 완료 되도록 변경하기 전에 몇 초 정도 기다려야 합니다. 앱이 시작되면 지오코더 조회가 시작 됩니다. view model의 테스트 인스턴스를 만들면 지오 코더 조회도 시작됩니다. 몇 초 정도 기다리면 테스트 조회를 시작하기 전에 다른 조회를 완료 할 수 있습니다. [Apple’s documentation](https://developer.apple.com/documentation/corelocation/clgeocoder/1423509-geocodeaddressstring)의 문서에 따르면 `CLLocation` 요청 비율(the rate of requests)이 너무 높으면 오류(error)가 발생할 수 있다고 경고합니다.
1. expectation이 충족 될 때까지 최대 8초 동안 기다립니다. 테스트는 예상 결과가 시간 초과 전에 도착한 경우에만 성공합니다.

```testChangeLocationUpdatesLocationName()``` 옆에 있는 다이아몬드를 클릭하여 데스트를 실행 하십시오. 테스트에 통과하면 다이아몬드가 녹색 확인 표시로 바뀝니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2019/12/runtest.gif" height="600">
</p>

위 예제와 같은 방법으로 ```WeatherViewModel```의 다른 값을 확인하는 테스트를 작성할 수 있습니다. weatherbit.io에 대한 종속성을 피하기 위해 모의(mock) weather service를 사용하는 것이 이상적 입니다.

## MVVM 리팩토링 복습/이점
[Reviewing The Refactoring to MVVM](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-010)

여기까지 수고 많았습니다. 지금까지의 리팩토링 작업 내용을 되돌아 보면 몇 가지 이점을 발견할 수 있습니다.

* **복잡성 감소(Reduced complexity)**: ```WeatherViewController```이 더 단순화 되었습니다.
* **전문화(Specialized)**: ```WeatherViewController```는 더 이상 어떤 모델 타입(model types)에도 종속되지 않고 오로지 view의 역할에 집중합니다.
* **분화(Separated)**: ```WeatherViewController```는 ```changeLocation(to:)```함수에서와 inputs을 보내는 작업, 혹은 outputs을 바인딩하는 작업 등 ```WeatherViewModel```과만 상호작용(interect)합니다.
* **우수한 표현력(Expressive)**: ```WeatherViewModel```은 비지니스 로직(business logic)과 하위 레벨 뷰 로직(the low level view logic)을 분리합니다.
* **우수한 유지보수(Maintainable)**: ```WeatherViewController```에 최소한의 수정만으로 기능을 추가할 수 있습니다.
* **용이한 태스트(Testable)**: ```WeatherViewModel```는 상대적으로 테스트하기 쉽습니다.

그러나, MVVM 사용 시에 고려해야 할 몇가지 상충관계(trade-offs)가 있습니다.
* **Extra type**: MVVM에서는 몇 가지 새로운 모델 타입(model type)을 사용해야 합니다.
* **Binding mechanism**: 이 튜토리얼에서 처럼(Box) 몇 가지 바인딩 수단을 사용해야 합니다.
* **Boilerplate**: MVVM을 구현하기 위해서는 몇 가지 보일러플레이트(boilerplate)가 필요합니다.
* **Memory**: 뷰 모델을 도입할 때는 반드시 메모리 관리(memory management)와 메모리 유지 주기(memory retain cycles)를 알고 있어야 합니다.

## 끝으로...
[Where to Go From Here](https://www.raywenderlich.com/6733535-mvvm-from-the-ground-up#toc-anchor-011)

글 상단의 **Download Materials** 버튼을 통해 완성된 프로젝트를 다운로드 할 수 있습니다.

MVVM은 이제 프로페셔널 iOS 개발자가 가져야하는 핵심 개발역략이 되었습니다. 많은 전문분야에서 MVVM에 익숙하고 이를 구현할 수 있어야 합니다. 반응형 프로그래밍(reactive programming)을 가능하게 하는 Apple의 Combine 프레임 워크를 도입하는 경우 특히 그렇습니다.

[Design Patterns By Tutorials](https://store.raywenderlich.com/products/design-patterns-by-tutorials)은 MVVM 패턴에 관한 더 많은 소스를 제공합니다.

만약 여러분이 Combine 프레임 워크와 MVVM 구현 방법에 대해 더 알고 싶다면, [MVVM with Combine](https://www.raywenderlich.com/228229/mvvm-with-combine-tutorial-for-ios) 튜토리얼이나 [Combine: Asynchronous Programming With Swift](https://www.raywenderlich.com/236269/combine-asynchronous-programming-with-swift-complete-book-available)책을 참고하십시오.

Key-Value 옵서빙에 대해서는 [What’s New in Foundation: Key-Value Observing](https://www.raywenderlich.com/179709/screencast-whats-new-foundation-key-value-observing-kvo)을 확인하십시오.

여러분이 이 튜토리얼과 함께 즐거운 경험이 됐길 바랍니다.

> 이 글에 대한 토론/댓글을 보거나 작성하실 분은 [클릭](https://forums.raywenderlich.com/t/ios-mvvm-tutorial-refactoring-from-mvc-raywenderlich-com/100270/2)하십시오.
