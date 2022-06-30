# Interpreter vs compiler




목적: 컴파일러와 인터프리터 각각의 특징을 알고 차이점을 설명할 수 있어야 한다.


## 0. 요약
|특징|컴파일러|인터프리터|
|--|--|--|
|동작|전체 파일을 스캔해 한번에 번역한다.|한 번에 한 문장씩 번역한다.|
|시간|초기 스캔 시간이 오래 걸리지만 한번 실행 파일이 만들어지고 나면 빠르다.|한번에 한문장씩 번역 후 실행하기 때문에 느리다.|
|메모리|더 많은 메모리를 사용한다.|메모리 효율이 좋다.|
|오류|전체 코드를 스캔하는 과정에서 오류를 출력하므로 실행 전에 오류를 알 수 있음|프로그램을 실행시키고 오류를 발견하면 실행을 중지한다.|
|대표언어|C,C++,JAVA |Python, Ruby, Javascript, JAVA|


## 1. 개요
고급 언어로 작성된 프로그램들을 실행하는 데에는 두 가지 방법이 있다. 하나는 프로그램을 컴파일 하는 것이고, 다른 하나는 프로그램을 인터프리터에 통과시키는 방법이다. 인터프리터는 고급 명령어들을 중간 형태로 변역한 다음, 그것을 실행한다. 이와는 대조적으로, 컴파일러는 고급 명령어들을 직접 기계어로 번역한다.


## 2. 컴파일러
- 소스코드로부터 기계어를 생성해내는 변환기.

컴파일러는<span style="color:yellow"> 프로그램 전체를 스캔하여 이를 모두 기계어로 번역한다.</span> 그래서 초기 스캔 시간은 오래 걸린다. 하지만 전체 실행 시간만 따지고 보면 인터프리터보다 빠른데, 그 이유는 컴파일러는 초기 스캔을 마치면 실행 파일을 만들어 놓고 다음에 실행할 때 만들어 놓았던 실행파일을 실행하기 때문이다. 컴파일러는 고급언어로 작성된 소스를 기계어로 번역하는 과정에서 <span style="color:yellow">오브젝트 코드(Object Code)</span>라는 파일을 만드는 데, 이 오브젝트 코드를 묶어서 하나의 실행 파일로 다시 만드는 <span style="color:yellow">링킹(Linking)</span> 이라는 작업을 한다. 이 때문에 컴파일러는 통상적으로 인터프리터보다 더 많은 메모리를 사용한다.

### 컴파일러의 실행 단계
1. 구문 분석: 소스 코드 파일을 읽어 문법요소 단위로 자른 후 이 문법을 해석하여 추상 구문 트리 생성
2. 최적화: 추상 구문 트리 분석 후 최적화 수행. 여기에서 도달 할 수 없는 코드를 식별하거나, 상수 표현식을 미리 계산해 두거나, 루프 풀기 등의 대부분의 최적화가 이 단계에서 수행된다.
3. 코드 생성: 최적화된 구문 트리로부터 목적 코드를 생성한다. 목표 언어가 기계어일 경우, 레지스터 할당, 연산 순서 바꾸기 등 하드웨어에 맞는 최적화가 이 단계에서 수행된다. 
4. 링킹: 목적 코드가 기계어일 경우, 여러 라이브러리 목적 코드를 묶어 하나의 실행 파일을 생성하게 된다.

## 3. 인터프리터
- 런타임 전 변환 과정을 거치지 않는, 소스 코드의 단계별 실행기
- 고급 언어로 작성된 원시코드 명령어들을 한번에 한 줄씩 읽어들여서 실행하는 프로그램. 그렇기 때문에 한번에 전체를 스캔하고 

인터프리터는 컴파일러와는 반대로 <span style="color:yellow">프로그램 실행 시 한 번에 한 문장씩 번역한다.</span> 그렇기 때문에 한번에 전체를 스캔하고 실행파일을 만들어서 실행하는 컴파일러보다 실행시간이 더 걸린다. 하지만 인터프리터는 메모리 효율이 좋다. 왜냐하면 컴파일러처럼 목적 코드를 만들지도 않고, 링킹 과정도 거치지 않기 때문이다.

참고로 인터프리터는 다음의 과정 가운데 적어도 한 가지 기능을 가진다.

1. 소스 코드를 직접 실행한다.
2. 소스 코드를 효율적인 다른 중간 코드로 변환하고, 변환한 것을 바로 실행한다.
3. 인터프리터 시스템의 일부인 컴파일러가 만든, 미리 컴파일된 저장 코드의 실행을 호출한다.


참고 자료:
1. [컴파일러와 인터프리터의 차이](https://blog.naver.com/PostView.naver?blogId=ehcibear314&logNo=221228200531&redirect=Dlog&widgetTypeCall=true&topReferer=https%3A%2F%2Fsearch.naver.com%2Fsearch.naver%3Fsm%3Dtab_hty.top%26where%3Dnexearch%26query%3D%25EC%259E%2590%25EB%25B0%2594%2B%25EC%259D%25B8%25ED%2584%25B0%25ED%2594%2584%25EB%25A6%25AC%25ED%2584%25B0%26oquery%3D%25EC%259D%25B8%25ED%2584%25B0%25ED%2594%2584%25EB%25A6%25AC%25ED%2584%25B0%2B%25EC%25BB%25B4%25ED%258C%258C%25EC%259D%25BC%26tqi%3Dhr6h%252BwprvmZssOpsNcNssssstcs-472022&directAccess=false)

2. [컴파일러와 인터프리터의 차이2](https://velog.io/@jhur98/%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%ACcompiler%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0interpreter%EC%9D%98-%EC%B0%A8%EC%9D%B4)

3. [자바 컴파일러와 인터프리터 둘 다 가진다](https://velog.io/@tsi0521/Java%EB%8A%94-%EC%BB%B4%ED%8C%8C%EC%9D%BC%EB%9F%AC%EC%99%80-%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0-%EB%91%98-%EB%8B%A4-%EA%B0%80%EC%A7%84%EB%8B%A4)