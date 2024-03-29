## 📌 프로그램(Program) 이란??

파일이 저장장치에 저장되어 있지만, 메모리에는 올라가있지않은 정적인 상태를 말한다.

→ 운영체제가 프로그램이 실행되기 위한 독립적인 메모리공간을 할당해 주지 않은 상태로, 아직 실행되지 않는 코드 덩어리를 의미한다. ex) exe, dmg 파일

- 어떤 작업을 위해 실행할 수 있는 파일

<br/>

## 📌 프로세스(Process) 란??

운영체제로 부터 자원을 할당받은 **작업의 단위**이다.

- 메모리에 올라와 실행되고 있는 프로그램의 인스턴스(독립적인 개체)
- 운영체제로부터 시스템 자원(독립된 메모리 영역)을 할당 받는 작업의 단위
- 동적인 개념으로는 컴퓨터에서 실행되고 있는 프로그램을 의미

프로세스는 각각 독립된 메모리영역 (Code, Data, Stack, Heap 의 구조)을 할당 받는다.

기본적으로 프로세스당 최소 1개의 스레드( 메인 스레드)를 가지고 있다.

각 프로세스는 별도의 주소공간에서 실행되며, 한 프로세스는 다른 프로세스의 변수나 자료구조에 접근할 수 없다.

한 프로세스가 다른 프로세스의 자원에 접근하려면 프로세스 간의 통신 (IPC(inter-process communication))을 사용해야한다. (ex. 파이프,파일, 소켓등을 이용한 통신방법)

![프로세스](https://user-images.githubusercontent.com/87989933/172766277-f56b1a44-e160-4a3d-b68a-d6b2feec5694.png)

- 한 프로그램을 처리하기 위해 여러개의 프로세스를 생성한다면???

→ 운영체제는 안전성을 위해 프로세스마다 자신에게 할당된 메모리 내의 정보에만 접근할 수 있도록 제약을 두고 있고, 이를 벗어나는 정보에 접근하려면 오류가 발생한다.

<br/>

## 📌 스레드(Thread) 란??

프로세스가 할당받은 자원을 이용하는 **실행 흐름의 단위**

- 프로세스 내에서 실행되는 여러 흐름의 단위
- 프로세스의 특정한 수행 경로

스레드는 프로세스 내에서 각각 Stack만 따로 할당받고, Code, Data, Heap 영역은 공유한다.

스레드는 한 프로세스 내에서 동작되는 여러 실행의 흐름으로, 프로세스 내의 주소공간이나 자원들(힙 공간 등)을 같은 프로세스 내에 스레드끼리 공유하면서 실행된다.

같은 프로세스 안에 있는 여러 스레드들은 같은 힙 공간을 공유한다. 반면에 프로세스는 다른 프로세스의 메모리에 직접 접근할 수 없다.

각각의 스레드는 별도의 레지스터와 스택을 갖고 있지만, 힙 메모리는 서로 읽고 쓸 수 없다.

한 스레드가 프로세스 자원을 변경하면, 다른 이웃 스레드(sibling thread)도 그 변경 결과를 즉시 볼 수 있다.

![스레드](https://user-images.githubusercontent.com/87989933/172766281-a755bc9e-3f27-41d1-a5f0-33552b52db7b.png)

### 프로세스 vs 스레드

하나의 프로세스를 실행하다 오류가 발생하여 프로세스가 강제로 종료된다면, 공유하고 있는 파일을 손상시키지 않는다면, 아무런 영향이 없다.

하지만, 스레드는 Code/Data/Heap 메모리 영역의 내용을 공유하기 때문에, 하나의 스레드에서 오류가 발생한다면 같은 프로세스 내의 다른 스레드 모두가 강제로 종료된다.

<br/>

### 자바 스레드(Java Thread) 란??

일반 스레드와 거의 차이가 없으며, JVM가 운영체제의 역할을 한다.

자바에는 프로세스가 존재하지 않고 스레드만 존재하며, 자바 스레드는 JVM에 의해 스케줄되는 실행 단위 코드 블록이다.

자바에서 스레드 스케줄링은 전적으로 JVM에 의해 이루어진다.

아래와 같은 스레드와 관련된 많은 정보들도 JVM이 관리한다.

- 스레드가 몇 개 존재하는지
- 스레드로 실행되는 프로그램 코드의 메모리 위치는 어디인지
- 스레드의 상태는 무엇인지
- 스레드 우선순위는 얼마인지

즉, 개발자는 자바 스레드로 작동할 스레드 코드를 작성하고, 스레드 코드가 생명을 가지고 실행을 시작하도록 JVM에 요청하는 일 뿐이다.

### 멀티 태스킹

OS를 통해 CPU가 작업하는데 필요한 자원을 프로세스 또는 스레드간에 나누는 행위를 말한다.

이를 통해 여러 응용프로그램을 동시에 열고 작업할 수 있다는 장점이 있다.

<br/>

# 멀티 프로세스 vs 멀티 스레드

## 📌 멀티 프로세스

하나의 응용프로그램을 여러개의 프로세스로 구성하여 각 프로세스가 하나의 작업을 처리하도록 하는 것이다.

**장점**

- 여러개의 프로세스 중 하나에 문제가 발생해도, 다른 프로세스에 영향이 확산되지 않는다.

**단점**

1. Context Switching에서의 오버헤드

- 캐시 메모리 초기화 등 무거운 작업이 진행되고 많은 시간이 소모되는 등의 오버헤드가 발생한다.
- 프로세스 사이에 공유하는 메모리가 없기때문에, Context Switching이 발생하면 캐시에 있는 모든 데이터를 리세하고 다시 캐시정보를 불러와야한다.

2. 프로세스 사이의 어렵고 복잡한 통신기법 (IPC)

- 독립된 메모리 영역을 할당 받았기 때문에, 하나의 프로그램에 속하는 프로세스들 사이의 변수를 공유할 수 없다.

### **Context Switching 이란??**

CPU에서 여러 프로세스를 돌아가면서 작업을 처리하는 과정이다.

동작 중인 프로세스가 대기 하면서 해당 프로세스의 상태(Context)를 보관하고, 대기하고 있던 다음 순서의 프로세스가 동작하면서 이전에 보관했던 프로세스의 상태를 복구하는 작업을 말한다.

<br/>

## 📌 멀티 스레드

하나의 응용 프로그램을 여러개의 스레드로 구성하고, 각 스레드로 작업을 처리하도록 하는 것이다.??

하나의 프로세스가 여러 작업을 여러 스레드를 사용하여 동시에 처리하는 것을 의미한다.??

웹 서버는 대표적인 멀티 스레드 응용 프로그램이다.

**장점**

1. Context-Switching 할 때 공유하고 있는 메모리만큼의 메모리 자원을 아낄 수 있다.
2. 처리비용감소 (실행속도 향상)
   스레드는 프로세스 내의 Stack 영역을 제외한 모든 메모리를 공유하기 때문에, 통신의 부담이 적어서 응답시간이 빠르다.

**단점**

1. 스레드 하나가 프로세스 내 자원을 망쳐버린다면 모든 프로세스가 종료될 수 있다.
2. 자원을 공유하기 때문에, 필연적으로 **동기화 문제(Synchronization Issue)**가 발생할 수 밖에 없다.

<br/>

### Thread safe

여러 스레드가 동시에 사용되어도 안전한 것을 의미한다.

<br/>

### 멀티 스레드를 선택하는 이유

간단하게, 프로그램을 여러개 키는 것보다 하나의 프로그램 안에서 여러 작업을 해결하는 것

- 멀티 프로세스로 실행되는 작업을 멀티 스레드로 실행할 경우, 프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어들어 자원을 효율적으로 관리할 수 있다.
  - 프로세스간의 Context Switching시에는 단순히 CPU 레지스터 교체 뿐만아니라 RAM, CPU사이의 캐시메모리에 대한 데이터까지 초기화 되므로 오버헤드가 크지만,
    스레드는 프로세스 내의 메모리를 공유하기 때문에, 스레드 간의 데이터를 주고받는 것이 간단해지고, 시스템 자원 소모가 줄어들게 된다.
- 프로세스 간의 통신(IPC)보다 스레드 간의 통신의 비용이 적으므로 작업들 간의 통신의 부담이 줄어든다.
  - 스레드는 Stack 영역을 공유하기 때문에, Context Switching시 Stack 영역만 처리한다.
    따라서, 프로세스간의 전환속도보다 스레드 간의 전환 속도가 빠르다.

<br/>

## 정리

프로세스와 스레드는 개념의 범위부터 다르다. 스레드는 프로세스 안에 포함되어 있기 때문이다.

운영체제가 프로세스에게 Code/Data/Stack/Heap 메모리 영역을 할당해 주고 최소 작업 단위로 삼는 반면, 스레드는 프로세스 내에서 Stack 메모리 영역을 제외한 다른 메모리 영역을 같은 프로세스 내 다른 스레드와 공유한다.

프로세스는 다른 프로세스와 정보를 공유하려면 IPC를 사용하는 등의 번거로운 과정을 거쳐야 하지만, 스레드는 기본 구조 자체가 메모리를 공유하는 구조이기 때문에 다른 스레드와 정보 공유가 쉽다. 때문에 멀티태스킹보다 멀티스레드가 자원을 아낄 수 있게 된다. 다만 스레드의 스케줄링은 운영체제가 처리하지 않기 때문에 프로그래머가 직접 동기화 문제에 대응할 수 있어야 한다.

<br/>

### 참고

[https://velog.io/@raejoonee/프로세스와-스레드의-차이](https://velog.io/@raejoonee/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%B0%A8%EC%9D%B4)
[https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html](https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)
