- *This article/tutoral is **permitted translation in Korean language by [raywenderlich.com](https://www.raywenderlich.com/)***

- *이 글/튜토리얼은 raywenderlich.com의 **허가를 받고 번역**하였습니다.*

- *전문 번역가의 번역이 아니며 **다소의 오역/의역 혹은 과감한 생략**이 있을 수 있음을 알려드립니다.*

-----

# iOS용 Alamofire 5 튜토리얼 시작하기

[Alamofire 5 Tutorial for iOS: Getting Started](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started) by [Corey Davis](https://www.raywenderlich.com/u/cordavis)

* **Version**: Swift 5, iOS 13, Xcode 11
* **Level**: Beginner
* [**Download Materials**](https://koenig-media.raywenderlich.com/uploads/2020/02/StarWarsOpedia.zip)

여러분이 iOS앱을 개발해 본 적이 있다면, 아마도 네트워크를 통해 데이터에 접근해야 할 일이 있었을 것입니다. Swift의 Foundation에서 제공하는 ```URLSession```을 사용했을 수도 있습니다. ```URLSession```은 괜찮은 기능을 제공하지만, 가끔씩 사용하기에 불편하기도 합니다. 그리고 그런 이유로 이 튜토리얼이 쓰여졌습니다.

**Alamofire**는 Swift를 기반(Swift-based)으로 만들어진 HTTP Networking 라이브러리입니다. Apple의 Foundation Networking 계층(stack) 위에서 일반적인 네트워킹 작업을 단순화하여 멋진 인터페이스(interface)를 제공합니다.

이 Alamofire 튜토리얼에서는 아래와 같은 네트워킹 작업을 연습하게 됩니다.
* 써드파티(a third-party) RESTful API에 데이터를 요청하기
* 요구 매개변수(request parameters) 보내기
* 응답 받은 데이터(response)를 JSON으로 변환하기
* 응답받은 데이터를 Codable protocol을 사용하여 Swift data 모델(model)로 변환하기

> 이 튜토리얼을 시작하기 전에, HTTP Networking에 대한 사전 이해가 필요합니다. 필수적인 것은 아닙니다만, Apple에서 구현한 네트워킹 관련 Classes가 도움이 될것 입니다. Alamofire의 상세한 구현사항은 조금 이해하기 어렵기 때문에 네트워크 요청 문제를 해결해야 하는 경우 배경 지식이 있는 것이 좋습니다.

## 시작하며...
[Getting Started](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-001)

튜토리얼을 시작하기 위해 이 글 상단의 **Download Materials**을 통해 프로젝트를 다운로드하고 begin 폴더의 프로젝트를 열겠습니다.

이 튜토리얼을 위한 프로젝트 **StarWarsOpedia**는 스타워즈 영화 데이터에 빠르게 접근가능하고 ~~우주선(starships)에도 접근가능 합니다~~.

빌드-런하면 아래와 같은 화면을 볼수 있습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2020/01/1-1.png" width="231">
</p>

거의 아무 것도 없는 백지상태입니다만 여러분이 그 공백을 채울 것입니다.

> 보통 Alamofire는 CocoaPods 혹은 또 다른 써드 파티 라이브러리 관리 도구(dependency manager)를 통해 프로젝트와 통합되어 집니다. 이 튜토리얼에서 Alamofire는 이미 다운로드 되어 프로젝트에 통합되어 있습니다. CocoaPods를 통해 Alamofire를 프로젝트에 통합하는 과정은 [CocoaPods Tutorial for Swift: Getting Started](https://www.raywenderlich.com/626-cocoapods-tutorial-for-swift-getting-started)를 참조해 주십시오.

## SW(스타워즈) API 사용하기
[Using the SW API](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-002)

**SW API**는 오픈 API이고 무료로 Star Wars 관련 데이터를 제공하며 주기적으로 업데이트 됩니다. Alamofire를 배우기 위한 흥미로운 방법이 될 것입니다. [ swapi.dev](https://swapi.dev/)에 접속해 보기 바랍니다.

SW API는 다양하고 상세한 데이터에 접근하는 것이 가능합니다만 이 튜토리얼에서는 두 API에 집중하겠습니다.
* [https://swapi.dev/api/films](https://swapi.dev/api/films)
* [https://swapi.dev/api/starships](https://swapi.dev/api/starships)

SW API에 대해 깊은 관심이 있다면 [Swapi documentation](https://swapi.dev/documentation)를 참고하십시오.

## HTTP, REST, JSON 이해하기
[Understanding HTTP, REST and JSON](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-003)

만약 여러분이 처음으로 인터넷을 통해 써드-파티 서비스에 접근하는 것이라면 아래의 (간단한) 설명은 도움이 될 것입니다. 

**HTTP**는 통신규약(application protocol)으로서 서버(server)에서 클라이언트(client)로 데이터를 전송하는 방법입니다. HTTP는 몇 가지 통신규약을 정의 하는데 클라이언테는 원하는 데이터를 요청하기 위해 해당 통신규약을 준수해야 합니다. 예를 들어,
* **GET**: Web page와 같이 서버로부터 데이터를 가져오는 방법 중 하나이며 서버에 존재하는 데이터를 변경하지 않습니다.
* **HEAD**: GET과 동일하지만, 실제 데이터가 아닌 헤더만 다시 보냅니다.
* **POST**: 서버쪽으로 클라이언트의 데이터를 보냅니다. 예를들어, 회원가입 폼(form)을 작성후 클릭하여 제출할 때 사용됩니다.
* **PUT**: 지정된 특정 위치(서버)로 데이터를 보냅니다. 예를 들어, 사용자의 프로필을 업데이트 할 때 사용하십시오.
* **DELETE**: 지정된 특정 위치(서버)에서 데이터를 삭제합니다.

**JSON**은 자바스크립트 표기법(JavaScript Object Notation)으로 나타냅니다. 직관적(straightforward)이고, 읽기쉬운(human-readable ) 전송 메카니즘을 가지고 서버-클라이언트 사이에서 데이터를 송수신 합니다. JSON은 string, boolean, array, object/dictionary, number, null과 같은 제한된 데이터 타입(data type)만이 사용 가능합니다. 

Swift 4 이전~~의 암흑기~~에는 JOSN을 데이터 오브젝트(data objects)로 혹은 그 반대로 변환하기 위해 ```JSONSerialization``` 클래스를 사용했었습니다. 이는 잘 작동했고 지금도 사용할 수 있지만, 지금은 ```Codable```이라는 더 나은 방법을 주로 사용합니다. ```Codable```을 준수하는 것으로, 거의 반자동적으로 JSON과 여러분 프로젝트의 데이터 모델(data models) 사이의 (혹은 그 반대로) 변환이 이루어 집니다.

**REST** 또는 **REpresentational State Transfer**는 일관된 Web API를 설계하계하기 위한 일종의 규칙의 세트(set of rules)입니다. REST는 몇가지 구조상의 표준화된 규칙을 가집니다. 
* **무상태성(Stateless)**: 각각의 요청(requests)을 별경의 것으로 인식 및 철리하며, 이전 요청이 다음 요청에 연관되지 않도록 할 것
* 요청을 **캐시 가능 하도록(Cacheable)** 할 것
* **동인한 인터페이스(Uniform interfaces)** 를 제공할 것

이러한 규칙은 API와의 앱 개발에 있어, 요청에 따른 데이터 상태의 추적없이 앱과 API의 통합을 더욱 쉽게 합니다.

HTTP, JSON 및 REST는 개발자가 사용할 수 있는 웹 서비스의 상당 부분을 구성합니다. 각 부분의 하나 하나가 어떻게 동작하는지 이해하는 것은 너무 과한 일입니다. 그리고 이 지점이 *왜 Alamofire를 사용해야 하는가*에 대한 대답일 수 있습니다.

## 왜 Alamofire를 사용해야 하는가?
[Why Use Alamofire?](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-004)

여러분은 혹시 왜 Alamofire를 사용해야 하는지에 대한 궁금증을 갖을 수 있습니다. Apple은 HTTP 프로토콜을 통해 APIs에 접근할 수 있도록 이미 ```URLSession```와 관련된 클래스를 제공함에도 불구하고 왜 또 다른 라이브러리(dependency)를 추가해야 할까요?

간단히 대답하자면, Alamofire 역시 URLSession을 기반으로 하지만 네트워킹 호출(networking calls), 앱 제작 그 자체(business logic)에 더욱 집중할 수 있기 때문입니다. 즉, 더 적은 노력으로 인터넷 데이터에 접근할 수 있으며 여러분의 소스코드는 더 읽기 쉽고 깨끗해 질 것입니다.

아래는 주요한 Alamofire의 함수입니다.
* **AF.upload**: 멀티-파트(multi-part), 스트림(stream), 파일 또는 데이터 방식으로 파일을 업로드 합니다.
* **AF.download**: 파일을 다운로드 하거나 또는 이미 다운로드 중인 작업을 재개합니다.
* **AF.request**: 파일 전송과 관련없는 HTTP 요청

위 함수는 전역 함수이기 때문에, 인스턴트화 하지 않고 사용할 수 있습니다. 이 외에도 Alamofire는 ```SessionManager```, ```DataRequest```, ```DataResponse```와 같은 다수의 클래스(classes)와 스트럭트(strucs)를 포함하고 있지만 시작하는 단계에서 Alamofire의 구조를 모두 완벽하게 이해할 필요는 없습니다.

이론적인 부분은 여기까지이며, 지금부터 코드 작성을 시작하겠습니다.

## 데이터 요청하기
[Requesting Data](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-005)

코드 작성을 시작하기에 앞서 몇 가지 셋업을 할 필요가 있습니다.

**MainTableViewController.swift** 파일을 열고 ```import UIKit```아래에 아래와 같이 코드를 추가 하십시오.

```swift
import Alamofire
```

이것으로 Alamofire를 view controller에서 사용할 수 있습니다. 계속해서 아래의 코드를 파일의 가장 아래 부분에 추가합니다.

```swift
extension MainTableViewController {
  func fetchFilms() {
    // 1
    let request = AF.request("https://swapi.dev/api/films")
    // 2
    request.responseJSON { (data) in
      print(data)
    }
  }
}
```

코드 해석 입니다.

1. Alamofire는 **namespacing**를 사용하므로 모든 호출의 앞서 접두사로 ```AF```를 사용합니다.. ```request(_:method:parameters:encoding:headers:interceptor:)```는 데이터 요청을 위해 엔드 포인트(endpoint) 정보를 담습니다. 사실 더 많은 매개변수를 지정할 수 있지만 여기에서는 필수 매개변수 값(default parameter values)인 URL만 지정하겠습니다.
1. 요청에 따른 응답(response)을 JSON 데이터 형태로 받습니다. 여기에서는 디버깅을 위해 응답받은 JSON을 간단히 print 하도록 하겠습니다.

마지막으로, ```viewDidLoad()```의 끝 부분에 아래의 코드를 추가합니다.

```
fetchFilms()
```

여러분이 방금 작성한 코드는 Alamofire request의 시발점이 될 것이며 빌드-런 하면 콘솔(console)에서 아래와 같은 화면을 볼 수 있을 것입니다.
```
success({
  count = 7;
  next = "<null>";
  previous = "<null>";
  results =  ({...})
})
```

단지 짧은 몇 줄의 코드이지만 성공적으로 서버로부터 JSON 데이터를 가져왔습니다. ~~Good job!~~

## Codable Data Model 사용하기
[Using a Codable Data Model](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-006)

이제 JSON 형태로 되돌아온 데이터를 어떻게 사용할지 알아보겠습니다. JSON 데이터는 계층적인 구조를 가지고 있기 때문에 아무런 가공없이 바로 사용하는 것은 아주 불편한 일일 것 입니다. 그러므로 모델(models)을 만들어서 데이터를 저장 하겠습니다.

프로젝트 네이게이터에서 **Networking**그룹 찾아 새로운 Film.swift 파일을 만듭니다. 그리고 다음 코드를 추가하겠습니다.
```swift
struct Film: Decodable {
  let id: Int
  let title: String
  let openingCrawl: String
  let director: String
  let producer: String
  let releaseDate: String
  let starships: [String]
  
  enum CodingKeys: String, CodingKey {
    case id = "episode_id"
    case title
    case openingCrawl = "opening_crawl"
    case director
    case producer
    case releaseDate = "release_date"
    case starships
  }
}
```
API의 film으로부터 가져온 데이터를 맵핑할 데이터 프로퍼티와 코딩 키(Coding keys)를 만들었습니다. 여기에서는 **어떻게** 스트럭트가 ```Decodable```이 되는지에 주목할 필요가 있습니다. 이 부분이 바로 JSON이 data model 바뀌는 부분입니다.

이 튜토리얼에서 나중에 보여줄 상세한 정보를 단순화 하기 위해, ```Displayable``` 프로토콜을 정의 하겠습니다. 이 부분에서 중요한 부분은 ```Film```이 Displayble을 준수하도록 하는 것 입니다. 다음 코드를 **Film.swift**의 끝 부분에 추가하겠습니다.

```swift
extension Film: Displayable {
  var titleLabelText: String {
    title
  }
  
  var subtitleLabelText: String {
    "Episode \(String(id))"
  }
  
  var item1: (label: String, value: String) {
    ("DIRECTOR", director)
  }
  
  var item2: (label: String, value: String) {
    ("PRODUCER", producer)
  }
  
  var item3: (label: String, value: String) {
    ("RELEASE DATE", releaseDate)
  }
  
  var listTitle: String {
    "STARSHIPS"
  }
  
  var listItems: [String] {
    starships
  }
}
```

위 extension을 사용하면 상세 정보를 표시하는 뷰 컨트롤러가 모델 자체에서 필름에 대한 올바른 라벨과 값을 가져올 수 있습니다.

Networking 그룹에서 Films.swift 파일을 만들어 아래 코드를 추가 하겠습니다.

```swift
struct Films: Decodable {
  let count: Int
  let all: [Film]
  
  enum CodingKeys: String, CodingKey {
    case count
    case all = "results"
  }
}
```

위 스트럭트는 영화 데이터 컬렉션을 의미합니다. 이전 콘솔에서 본 것과 같이, **swapi.dev/api/films**는 네 가지 주요한 값 ```count```, ```next```, ```previous```, ```results```를 보냅니다. 이 앱에서는 ```count```와 ```results```만을 사용할 예정이므로 ```next```, ```previous``` 프로퍼티는 생성하지 않겠습니다.

CodingKeys에서 정의한대로, 서버에서 ```results``` 로 이름 붙여진 데이터는 코드에서 ```all```이라는 변수명으로 바뀌어 사용됩니다. 다시 한번 강조하겠습니다. data model이 ```Decodable```을 준수하는 것으로서 Alamofire는 JSON 데이터를 여러분이 작성한 data model로 변환됩니다.

> ```Codable```에 대해서 보다 자세히 알고자 한다면, [Encoding and Decoding in Swift](https://www.raywenderlich.com/3418439-encoding-and-decoding-in-swift) 튜토리얼을 참조 하십시오.

이제 **MainTableViewController.swift** 팔일로 이동해서 ```fetchFilms()```의 코드를...

Before
```swift
request.responseJSON { (data) in
  print(data)
}
```

아래와 같이 바꾸겠습니다.

After
```swift
request.responseDecodable(of: Films.self) { (response) in
  guard let films = response.value else { return }
  print(films.all[0].title)
}
```

응답(response) 받은 내용을 JSON으로 변환하는 대신에, data model로 변환했으며, 디버깅을 위해 첫번 째 타이틀을 콘솔에 프린트(print) 했습니다.

빌드-런하여 첫 번째 타이틀이 무엇인지 확인하십시오. 다음은 모든 영화 데이터를 표시하는 작업을 이어가겠습니다.

<p align="center">
  <img src="https://koenig-media.raywenderlich.com/uploads/2020/01/2-1-650x108.png" width="650">
</p>

## 메서드 체이닝
[Method Chaining](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-007)

Alamofire는 메서드 체이닝(Method Chaining)을 사용합니다. 한 메서드의 결과값 혹은 객체를 통해 다른 함수를 호출하는 방법입니다. 이를 통해 코드를 더 간결하고 깔끔하게 유지할 수 있습니다.

```fetchFilms()```의 코드를 아래와 같이 바꿈으로서 직접 메서드 체이닝을 경험해 보겠습니다.
 
```swift
AF.request("https://swapi.dev/api/films")
  .validate()
  .responseDecodable(of: Films.self) { (response) in
    guard let films = response.value else { return }
    print(films.all[0].title)
  }
```

위 단일행은 이전에 여러 행으로 수행하던 작업을 정확하게 수행할 뿐만 아니라 유효성 검사도 추가했습니다.

코드 분석입니다. films API에 데이터를 요청했고, HTTP status code 200에서 299까지 체크함으로서 유효성 체크를 함으로서 응답받은 데이터(response)가 유효함을 확실히 했으며, 마지막으로 응답 받은 데이터를 만들어둔 데이터 모델(data model)로 디코드(decode) 했습니다. Nice! :]

## 테이블 뷰 준비하기
[Setting up Your Table View](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-008)
