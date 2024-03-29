# HTTP 상태코드

클라이언트가 보낸 HTTP 요청에 대한 서버의 응답코드로, 상태 코드에 따라 요청의 성공/실패 여부를 판단한다.  
상태코드의 첫번째 숫자는 응답의 클래스를 정의한다.

**1xx(정보)** : 요청을 받았으며 프로세스를 계속 진행  
**2xx(성공)** : 요청을 성공적으로 받았으며 인식했고 수용  
**3xx(리다이렉션)** : 요청 완료를 위해 추가 작업 조치가 필요  
**4xx(클라이언트 오류)** : 요청의 문법이 잘못되었거나 요청을 처리할 수 없음  
**5xx(서버 오류)** : 서버가 명백히 유효한 요청에 대한 충족을 실패

## 📌 1xx - Information response

상태 코드가 '1'로 시작하는 경우는 **서버가 요청을 받았으며, 서버에 연결된 클라이언트는 작업을 계속 진행하라는 의미이다.**

### 100 - Continue

진행중임을 의미하는 응답코드이다. 클라이언트가 계속해서 요청하거나 이미 요청완료한 경우에는 무시해도 되는 것을 알려준다.

### 101 - Switcing Protocol

이 응답 코드는 클라이언트가 보낸 Upgrade 요청 헤더에 대한 응답에 들어가며, 서버에서 프로토콜을 변경할 것임을 알려준다. 해당 코드는 Websocket 프로토콜 전환 시에 사용된다.

### 102 - Processing (WebDAV)

서버가 요청을 수신하였으며 이를 처리하고 있지만, 아직 제대로 된 응답을 알려줄 수 없음을 알려준다.

### 103 - Early Hints

주로 Link 헤더와 함께 사용되어 서버가 응답을 준비하는 동안 사용자 에이전트가(user agent) 사전 로딩(preloading)을 시작할 수 있도록 한다.

<br/>

## 📌 2xx - Successful response

상태코드가 '2'로 시작하는 경우는 **클라이언트의 요청이 성공적으로 수행되었다는 의미이다.**

### 200 - OK

요청이 성공적으로 수행되었다.

### 201 - Created

요청이 성공적이었으며 그 결과로 새로운 리소스가 생성되었다. 이 응답은 일반적으로 POST 요청 또는 일부 PUT 요청 이후에 따라온다.

### 202 - Accepted

요청을 수신하였지만 그에 응하여 행동할 수 없다. 이 응답은 요청 처리에 대한 결과를 이후에 HTTP로 비동기 응답을 보내는 것에 대해서 명확하게 명시하지 않는다.  
이것은 다른 프로세스에서 처리 또는 서버가 요청을 다루고 있거나 배치 프로세스를 하고 있는 경우를 위해 만들어졌다.
즉, 서버가 요청을 접수했지만 아직 처리하지 않았다.

### 203 - Non-Authoritative Information

서버가 요청을 성공적으로 처리했지만 다른 소스에서 수신된 정보를 제공하고 있다.

### 204 - No Content

요청에 대해서 보내줄 수 있는 콘텐츠가 없지만, 헤더는 의미있을 수 있다. 사용자-에이전트는 리소스가 캐시 된 헤더를 새로운 것으로 업데이트할 수 있다.

<br/>

## 📌 3xx - Redirection messages

상태코드가 '3'로 시작하는 경우는 **클라이언트가 요청을 완료하기 위해 추가 조취를 취해야 한다는 의미이다.**

### 301 - Moved Permanently

요청한 리소스의 URI가 변경되었음을 의미한다. 새로운 URI가 응답에서 아마도 주어질 수 있다.

### 304 - Not Modified

캐시를 목적으로 사용된다. 클라이언트에게 응답이 수정되지 않았음을 알려주며, 그러므로 클라이언트는 계속해서 응답의 캐시 된 버전을 사용할 수 있다.

<br/>

## 📌 4xx - Client error response

상태코드가 '4'로 시작하는 경우는 **클라이언트로 인한 오류가 발생했다는 의미이다.**

### 400 - Bad Request

잘못된 문법으로 인하여 서버가 요청을 이해할 수 없음을 의미한다.

### 401 - Unauthorized

의미상 이 응답은 **"비인증(unauthenticated)"** 을 의미한다.  
클라이언트는 요청한 응답을 받기 위해서는 반드시 스스로를 인증해야 한다. 로그인을 실패한 경우 또는 로그인하지 않는 사용자가 회원의 기능을 사용하려 할 때 사용한다.

### 403 - Forbidden

클라이언트가 콘텐츠에 접근할 권리를 가지고 있지 않다.  
ex) 미결제로 서비스를 이용할 수 없음  
**401과 다른 점은 서버가 클라이언트가 누구인지 알고 있다.**

### 404 - Not Found

서버가 요청받은 리소스를 찾을 수 없다. 브라우저에서는 알려지지 않은 URL을 의미한다. 이것은 API에서 종점(URI)은 적절하지만 리소스 자체는 존재하지 않음을 의미할 수도 있다.

### 405 - Method Not Allowed

요청에 지정된 메서드가 URI로 표시된 리소스에 허용되지 않음을 의미한다.

### 408 - Request Timeout

이 응답은 요청을 한지 시간이 오래된 연결에 일부 서버가 전송한다.

### 409 - Conflict

이 응답은 요청이 현재 서버의 상태와 충돌될 때 보낸다.

<br/>

## 📌 5xx - Server error response

상태코드가 '5'로 시작하는 경우는 **서버로 인한 오류 발생을 의미힌다.**

### 500 - Internal Server Error

내부 서버오류로, 서버에 오류가 발생하여 요청을 수행할 수 없음을 의미한다.

### 501 - Not Implemented

서버에 요청을 수행할 수 있는 기능이 없다. 예를 들어 서버가 요청 메소드를 인식하지 못할 때 이 코드를 표시한다.

### 502 - Bad Gateway

서버가 게이트웨이나 프록시 역할을 하고 있거나 또는 업스트림 서버에서 잘못된 응답을 받았다.

### 503 - Service Unavailable

서버가 오버로드되었거나 유지관리를 위해 다운되었기 때문에 현재 서버를 사용할 수 없다. 이는 대개 일시적인 상태이다.

### 504 - Gateway Timeout

서버가 게이트웨이나 프록시 역할을 하고 있거나 또는 업스트림 서버에서 제때 요청을 받지 못했다.
