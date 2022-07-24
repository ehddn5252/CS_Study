# API 란??

**Application Programming Interface**의 약자로,

응용 프로그램(애플리케이션)에서 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 말한다.

API는 리모컨과 점원처럼 애플리케이션과 운영체제 그리고 애플리케이션과 프로그래밍 언어가 제공하는 기능 사이의 '상호 작용'을 도와준다.

<br/>

# REST API 란??

**REpresentational State Transfer**의 약자로,

하나의 URI는 하나의 고유한 리소스를 대표하도록 설계된다는 개념에  
전송방식을 결합해서 원하는 작업을 지정한다.

웹상에서 사용되는 여러 리소스를 HTTP URI로 표현하고,  
해당 리소스에 대한 행위를 **HTTP Method (GET, POST, PUT, DELETE)** 로 정의하는 방식을 말한다.

\* 리소스는 JSON, XML과 같은 여러가지 언어로 표현할 수 있다.

## REST의 구성

- 자원 (Resource) - URI
- 행위 (Verb) - HTTP Method
- 표현 (Representations)

## REST 특징

- HTTP URI를 통해 제어할 자원(Resource)을 명시하고,  
  HTTP Method(GET/ POST/ PUT/ DELETE)를 통해  
   해당 자원(Resource)을 제어하는 명령을 내리는 방식의 Architecture이다.

- 기존 전송방식과 달리 서버는 요청으로 받은 리소스에 대해 순수한 데이터를 전송한다.

<br/>

### 기존 Service vs REST Service

- 기존 Service

  요청에 대한 처리를 한 후 가공된 data를 이용하여 **특정 플랫폼에 적합한 형태의 View**로 만들어서 반환한다.

  기존의 웹 접근 방식은 GET과 POST만으로 자원에 대한 CRUD를 처리하고, URI는 액션을 나타낸다.

- REST Service

  data 처리만 하거나, 처리 후 반환될 data가 있다면, JSON이나 XML 형식으로 전달한다. View에 대해서는 신경 쓸 필요가 없다.

  4가지 method를 사용하여 CRUD를 처리하고, URI는 제어하려는 자원을 나타낸다.

<br/>

# RESTful API 란?

RESTful API는 REST API 설계 가이드를 따라 API를 만드는것으로,  
RESTful API는 그 자체만으로도 API의 목적이 무엇인지 쉽게 알 수 있다.

## REST API의 설계 가이드

- 리소스에 대한 행위는 HTTP Method(POST, GET, PUT, DELETE)로 표현해야 한다.

- /(슬래시)는 계층 관계를 나타낸다.  
   URI 마지막에 /(슬래시)를 사용하지 않는다.

- URI에 \_(underscore) 대신 - 을 사용한다.

- URI에 동사는 GET, POST와 같은 HTTP Method를 표현하기 때문에, 동사보다는 명사를 사용하고, 대문자보다는 소문자를 사용한다.  
  \* 컨트롤 자원을 의미하는 경우 예외적으로 동사를 허용

- URI에 파일의 확장자(예를들어 .json , .JPGE)를 포함 시키지 않는다.
