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

> 이 튜토리얼을 시작하기 전에, HTTP Networking에 대한 사전 이해가 필요합니다. 필수적인 것은 아닙니다만, Apple에서 구현한 네트워킹 관련 Classes가 도움이 될것 입니다. Alamofire의 상세한 구현사항은 조금 이해하기 여렵기 때문에 네트워크 요청 문제를 해결해야 하는 경우 배경 지식이 있는 것이 좋습니다.

## 시작하며...
[Getting Started](https://www.raywenderlich.com/6587213-alamofire-5-tutorial-for-ios-getting-started#toc-anchor-001)

