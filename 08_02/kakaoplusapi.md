KAKAO 플러스친구 자동응답 API 구현하기
=======
KAKAO 플러스친구 자동응답 API를 구현해서 우리 자신만의 챗봇을 만들어봅시다.
## 1. API란?
API는 (Application Programming Interface, 응용 프로그램 프로그래밍 인터페이스)의 약자이며 응용프로그램에서 사용할 수 있도록 운영체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻합니다.<br> 이게 기본적인 사전적 정의고 우리가 사용하고 만들 API는 웹 API이며 이는 웹 어플리케이션에서 다른 서비스에 요청을 보내고 응답을 받기 위해 정의된 명세를 말합니다. 즉 다른 웹 서비스에서 요청이나 응답을 받을 수 있게 해주는 거죠.
## 2. WEB API 예시
> 네이버 지도 API -> 약도를 넣을 수 있다. <br>
기상청 날씨 API -> 날씨 정보를 집어 넣을 수 있다. <br>
카카오 로그인 API -> 카카오 아이디로 로그인 할 수 있다.<br>

이렇게 기존 서비스의 기능들을 요청하여 자신의 서비스에 삽입할 수 있습니다.
## 3. 요청??
그렇다면 우리는 어떻게 요청을 하게 될까요?<br>
일반적으로 클라이언트에서 서버로 요청하는 방식과 같습니다.<br>
우리는 URL을 통해서 요청을 보냈습니다.<br>
API도 마찬가지로 특정 요청 주소와 요청 값 이러한 방식으로 이루어져 있습니다.
## 4. 응답!!
그렇다면 응답을 어떻게 받을까요?<br>
우리가 개발했을때는 자동으로 응답 내용이 화면에 뿌려졌습니다.<br>
쉽게 말하자면 이미 정적으로 생성되어있는 화면에 동적으로 구해진 값이 합쳐져서 화면에 표현되었죠.<br>
<strong>일반 서비스 응답 = 정적인 화면 + 동적인 응답 값</strong><br>
하지만 API에서의 응답은 정적인 화면을 넘겨줄 필요가 없습니다.<br>
요청에 대한 응답만 생각하면 되는 거죠.<br>
그래서 응답 값만 넘어가는데 아무런 형식 없이 넘어가게되면 받는 사람 입장에서 굉장히 화가 날 겁니다.<br>
그래서 JSON,XML 등등의 많은 형식들이 만들어졌는데 우리는 이번에 JSON이란 형식으로 API를 만들어 보겠습니다.
## 5. JSON?
기본적으로 json은 익숙한 형태일겁니다. 우리가 사용했던 hash와 array가 JSON의 형태입니다.<br>
상세한 내용은 [링크](http://www.json.org/json-ko.html)를 참조하세요.
```json
{"key1":"value1","key2":"value2"}
```
우리가 구현할 rails 에서는 따로 json의 형태를 고민할 필요가 없습니다.<br>
후에 보여드리겠지만 hash로 만들고 자동 변환시켜주면 됩니다.
## 6. KAKAO 플러스친구 자동응답 API
그렇다면 이제 직접 우리가 API를 만들어보겠습니다.<br>
아까 API가 뭐라고 했죠? 다른 서비스에 요청을 보내고 응답을 받는거라고 했습니다.<br>
KAKAO 플러스친구 자동응답 API는 카카오톡에서 우리에게 요청을 보내고 그 응답을 받을 API를 만드는 겁니다.<br>
###  1. 카카오 플러스 친구 만들기
먼저 [카카오 플러스 친구](https://center-pf.kakao.com/login)에 접속하셔서 카카오 계정으로 로그인해주세요. <br>
그 후 새 플러스 친구 만들기를 통해 자신만의 플러스 친구를 만들어줍니다.
만든 후 접속 해서 왼쪽 스마트 채팅에 APi형을 클릭하면 준비작업이 끝납니다.<br>
api [document](https://github.com/plusfriend/auto_reply)는 이것을 참고해주세요.
### 2. document 읽어 보기
document를 읽어보면 만들수 있는 요청이 있습니다.<br>
가장 기본적으로 플러스 친구 접속했을 때 사용할 "/keyboard"와 메세지를 받았을 때의 "/message" 응답 등이 있습니다. <br>
이 두 응답을 가지고 서비스를 구현해보도록 하겠습니다.
### 3. "/keyboard" 만들기
크게 두가지 형태로 만들 수 있습니다.
1. 버튼 형태
```
{
    "type" : "buttons",
    "buttons" : ["선택 1", "선택 2", "선택 3"]
}
```
2. 직접 입력 형태
```
{
    "type" : "text"
}
```
3. "/message" 만들기
여러가지 형태로 만들 수 있지만 크게 두 부분으로 나뉩니다. "message","keyboard"(이는 아까 "/keyboard")
```
{
    "message": {
      "text": "귀하의 차량이 성공적으로 등록되었습니다. 축하합니다!",
      "photo": {
        "url": "https://photo.src",
        "width": 640,
        "height": 480
      },
      "message_button": {
        "label": "주유 쿠폰받기",
        "url": "https://coupon/url"
      }
    },
    "keyboard": {
      "type": "buttons",
      "buttons": [
        "처음으로",
        "다시 등록하기",
        "취소하기"
      ]
    }
}
```
