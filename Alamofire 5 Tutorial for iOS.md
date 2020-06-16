- *This article/tutoral is **permitted translation in Korean language by raywenderlich.com***

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

**REST**


