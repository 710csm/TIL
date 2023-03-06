# 네트워크 통신

## Rest API란
1. REST
    - Representational State Transfer의 약자로 자원을 이름으로 구분하여 해당 자원의 상태를 주고받는 모든것을 의미
    - HTTP URI(Uniform Resource Identifier)를 통해 자원을 명시하고, HTTP Method를 통해 해당 자원에 대한 CRUD Operation을 적용하는 것을 의미한다.
        1. HTTP URI에는 URL과 URN 두개가 있다
    - 즉, REST는 자원 기반의 구조 설계의 중심에 Resource가 있고 HTTP Method를 통해 Resource를 처리하도록 설계된 아키텍쳐를 의미한다.
2. REST API
    - REST 기반으로 서비스 API를 구현한 것
3. RESTful
    - REST라는 아키텍쳐를 구현하는 웹 서비스를 나타내기 위해 사용되는 용어이다. REST API를 제공하는 웹 서비스를 RESTful 하다고 할 수 있다. 하지만 REST를 사용했다고 모두가 RESTful한 것은 아니다. REST API의 설계 규칙을 올바르게 지킨 시스템을 RESTful 하다 말할 수 있다.

## Http 통신
- Client의 요청이 있을 때만 서버가 응답하여 해당 정보를 전송하고 곧바로 연결을 종료하는 방식
- 이러한 방식은 client가 요청을 보낸 경우에만 server가 응답하기 때문에 단방향적 통신으로 볼 수 있습니다. 따라서 server가 client로 요청을 보낼 수는 없습니다.
- 요청을 보낼 때, 내용을 기다리는 시간과 함께 연결하는 시간이 들어가게 됩니다. 이러한 Http 통신은 실시간 연결이 아닌, 필요한 경우에만 server로 접근하는 콘텐츠 위주의 데이터를 사용할 때 용이합니다.  만약 게시물에 대한 내용을 요청하기 위해 실시간으로 연결을 유지하는 Socket 통신을 사용하게 되면, 게시물을 받은 후에도 계속 통신을 위한 연결이 성립되어 있어 부하가 걸리게 됩니다.
- 일반적으로 모바일 어플리케이션은 필요한 경우에만 server로 정보를 요청하는 경우가 많은데, 이러한 web server로 Http 통신을 주로 사용하며 비용 및 유지보수 등 대부분의 방면에서 좋습니다.     
![f1](https://user-images.githubusercontent.com/45002556/108620020-579b0800-746c-11eb-9051-18df4b2bf574.png)     
![f2](https://user-images.githubusercontent.com/45002556/108620021-58cc3500-746c-11eb-8234-0d9c739abb73.png)

1. client가 요청을 보내는 경우에만 server가 응답하는 단방향 통신이다.
2. server로 부터 응답을 받은 후에는 연결이 바로 종료된다.
3. 실시간 연결이 아니고, 필요한 경우에만 server로 요청을 보내는 상황에 유용하다.
4. 요청을 보내 server로 응답을 기다리는 어플리케이션(Android or iOS)의 개발에 주로 사용된다.

## Socket 통신
- Server와 Client가 특정 port를 통해 실시간으로 양방향 통신을 하는 방식
- Socket 통신은 Http 통신과 달리 server와 client가 특정 port를 통해 연결을 성립하고 있어 실시간으로 양방향 통신을 하는 방식입니다.
- client만 필요한 경우에 요청을 보내는 Http 통신과 달리 Socket 통신은 server 역시 client로 요청을 보낼 수 있으며, 계속 연결을 유지하는 연결 지향형 통신이기 때문에 실시간 통신이 필요한 경우에 자주 사용됩니다.
- 예를 들면, 실시간 streaming 중계나 실시간 채팅과 같이 즉각적으로 정보를 주고받는 경우에 사용합니다.
- 예를 들어 실시간 동영상 streaming 서비스를 Http 통신이 구현하였다고 가정하겠습니다. 이러한 경우에 사용자가 서버로 동영상을 요청하기 위해서는 동영상이 종료되는 순간까지 계속 Http 통신을 보내야 하고 이러한 구조는 계속 연결을 요청하기 때문에 부하가 걸리게 됩니다. 그러므로 Socket 통신으로 구현하는 것이 적절합니다.
![f3](https://user-images.githubusercontent.com/45002556/108620022-5964cb80-746c-11eb-8baf-ec8001bf79f5.png)

### Socket 통신의 특징    
1. server와 client가 계속 연결을 유지하는 양방향 통신이다.
2. server와 client가 실시간으로 데이터를 주고받는 상황이 필요한 경우에 사용된다. 
3. 실시간 동영상 Streaming이나 온라인 게임 등과 같은 경우에 자주 사용된다.

## OAuth에 개념 설명
1. 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는 접근 위임을 위한 개방형 프로토콜이다.
2. OAuth를 사용하면 이 인증을 공유하는 애플리케이션끼리는 별도의 인증이 필요없다. 따라서 여러 애플리케이션을 통합해 쓰는것이 가능해진다.
3. 예를 들면 웹 서핑을 하다 Google과 Facebook 및 Twitter 등의 외부 소셜 계정을 기반으로 간편히 회원가입을 및 로그인 할 수 있는 웹 애플리케이션을 쉽게 찾아볼 수 있다. 클릭 한번으로 간편하게 로그인 할 수 있을 뿐만 아니라, 연동되는 외부 웹 애플리케이션에서 Facebook 및 Twitter 등이 제공하는 기능을 간편하게 사용할 수 있다는 장점이 있습니다. 예를 들어, 외부 웹 애플리케이션에 Google로 로그인하면 API를 통해 연동된 계정의 Google Calendar 정보를 가져와 사용자에게 보여줄 수 있습니다.
4. 인증 절차 종류
    - Authorization Code Grant
    - Implicit Grant
    - Resource Owner Password Credentials Grant
    - Client Credentials Grant

## http1 vs http2의 차이
1. HTTP/1.1은 기본적으로 연결당 하나의 요청과 응답을 처리하기 때문에 동시전송 문제와 다수의 리소스를 처리하기에 속도와 성능 이슈를 가지고 있다. 그래서 HOL(Head Of Line) Blocking-특정 응답지연, RTT(Round Trip Time) 증가, 헤비한 헤더 구조라는 문제점을 가지고 있습니다.
2. HTTP2는 성능뿐만 아니라 속도면에서도 월등하다. Multiplex Streams(한 커넥션에 여러개의 메세지를 동시에 주고 받을 수 있음), Stream Prioritization(요청 리소스간 의존관계를 설정), Server Push 등을 사용하여 성능을 획기적으로 향상 시켰다

<img width="658" alt="스크린샷 2022-08-17 오전 11 38 33" src="https://user-images.githubusercontent.com/45002556/222997614-48ba6853-7573-4f06-b4e4-649ba15d0df2.png">

## HTTP의 GET/POST의 차이점
- GET: 요청을 전송할 때 필요한 데이터를 Body에 담지 않고, 쿼리스트링을 통해 전송한다. URL의 끝에 ?와 함께 이름과 값으로 쌍을 이루는 요청 파라미터를 쿼리스트링이라 부른다. 요청 파라미터가 여러개면 &로 연결한다.
    1. GET은 캐시가 가능하다.
    2. GET 요청은 브라우저 히스토리에 남는다.
    3. GET 요청은 길이 제한이 있다.
    4. GET 요청은 중요한 정보를 다루면 안된다.
- POST: 리소스를 생성/변경하기 위해 설계 되었기 때문에 GET과 달리 전송해야할 데이터를 HTTP 메소드의 Body에 담아서 전송합니다. HTTP 메시지의 Body는 길이의 제한없이 데이터를 전송할 수 있습니다. 따라서 POST는 대용량 데이터를 전송할 수 있다. 그리고 POST로 요청을 보낼 때는 요청 헤더의 Content-Type에 요청 데이터의 타입을 표시해야 한다.
    1. POST 요청은 캐시되지 않는다.
    2. POST 요청은 브라우저 히스토리에 남지 않는다.
    3. POST 요청은 데이터 길이에 제한이 없다.
