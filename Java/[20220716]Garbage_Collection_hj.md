# 가비지 컬렉션 (Garbage Collection) 이란??

자바의 메모리 관리 방법 중의 하나로 **JVM의 Heap 영역에서 동적으로 할당했던 메모리 영역 중 필요없게 된 메모리 영역을 주기적으로 삭제하는 프로세스**

C나 C++에는 프로그래머가 수동으로 메모리 할당과 해제를 일일이 해줘야 하는 반면,  
Java는 JVM에 탑재되어 있는 가비지 컬렉터가 메모리 관리를 대행해 주기 때문에,  
**개발자 입장에서 메모리 관리, 메모리 누수 (Memory Leak) 문제에 대해 완벽하게 관리하지 않아도 되어 개발에만 집중할 수 있게 된다.**

## Garbage 란??

```java
Person person = new Person("Son");
person.hi();

person = new Person("Hyo");
person.hi();
```

"Son" 객체가 생성된 이후 person 변수에 의해 참조되는데, 이후 "Hyo" 객체가 생성되고 person 변수가 새로 생성된 "Hyo" 객체를 참조한다. 이때 "Son" 객체는 어떠한 경로로도 참조되지 않기 때문에 "Unreachable" 상태라고 하며, 이 객체는 가비지로 판단한다.

**-> 객체의 주소값을 가지고 있는 변수가 없으면, 가비지 컬렉션은 접근할 수 없는 객체 (Garbage)라고 판단한다.**

<br/>

## 📌 가비지 컬렉션의 대상이 되는 객체들

객체들은 Heap영역에서 생성되고, Method Area이나 Stack Area 등 Root Area에서는 Heap Area에 생성된 객체의 주소만 참조하는 형식으로 구성된다.  
하지만, 이렇게 생성된 Heap Area의 객체들이 메서드가 끝나는 등의 특정 이벤트로인해 Heap Area 객체의 메모리 주소를 가지고 있는 참조변수가 삭제되어, **Heap 영역에서 어디도 참조하고 있지 않은 객체들 (Unreachable) 이 발생하게 된다.** 이러한 객체들을 주기적으로 가비지 컬렉터가 제거해준다.

## 📌 가비지 컬렉션 동작 방식

### 1. Stop The World

가비지 컬렉션을 수행하기 위해 JVM이 애플리케이션의 실행을 일시 정지하는 것을 말한다. 가비지 컬렉션이 실행되면 GC 작업을 맡은 스레드를 제외한 나머지 스레드는 모두 멈추게 되고 GC 작업이 종료되면 재개된다.

### 2. Mark and Sweep

![image](https://user-images.githubusercontent.com/87989933/179281175-82659244-5e43-4040-86ec-3c65e3926546.png)

- **Mark** : 먼저 Root로부터 그래프 순회를 통해 연결된 객체를 찾아내어 각각 어떤 객체를 참조하는지 찾아서 마킹한다.

- **Sweep** : 참조하고 있지 않은 객체 즉, Uncreachable 객체들을 Heap에서 제거한다.

- **Compact** : Sweep 후에 분산된 객체들을 Heap의 시작 주소로 모아 메모리가 할당된 부분과 그렇지 않은 부분을 압축한다.

<br/>

## 📌 Heap Area 구조 & Minor GC, Major GC

![image](https://user-images.githubusercontent.com/87989933/179336567-7da27476-e7a3-49d0-91b1-41c86405feb8.png)
Heap Area는 효율적인 GC를 위해 크게 3가지의 영역으로 구분된다.

### **Young Generation 영역**

**Young Generation 영역**은 Heap 영역에 객체가 생성되면 최초로 Eden 영역에 age-bit 0으로 할당된다.
이 Young Generation에서 객체가 사라질 때 **Minor GC**라고 한다. 이 age-bit은 Minor GC에서 살아남을 때마다 1씩 증가하게 된다.  
 그리고 이 영역에 데이터가 어느정도 쌓이게 되면 참조정도에 따라 Survivor의 빈 공간으로 이동되거나 회수된다.

### **Old Generation 영역**

이 과정을 반복하다보면 age-bit이 JVM에서 설정해놓은 age-bit에 도달하게 되면, 오랫동안 쓰일 객체라고 판단하고 Old Generation 영역으로 이동시킨다.  
 이 과정을 **프로모션 (Promotion)** 이라고 한다.

Old generation 영역에서 객체가 사라지며, 메모리를 회수하는 GC를 **Major GC** 라고 한다.  
Major GC는 시간이 오래 걸리는 작업이고 이때 GC를 실행하는 스레드를 제외한 모든 스레드는 작업을 멈추게 된다. 이를 'Stop-the-World' 라 한다.

<br/>

## ❗️ 가비지 컬렉션의 장,단점

### 장점

1. 개발자가 동적으로 할당된 메모리를 관리할 필요가 없어지고, 메모리 누수 현상을 방지할 수 있다.

### 단점

1. 개발자는 메모리가 언제 해제되는지(GC가 언제 수행되는지) 정확하게 알 수 없다.

2. 가비지 컬렉션이 동작하는 동안에는 다른 동작을 멈추기 때문에 오버헤드가 발생한다. (Stop-the-world)

   이로인해 GC가 너무 자주 실행되면 소프트웨어 성능 하락의 문제가 되기도 한다.  
   잠깐의 소프트웨어 일시정지로도 목표한 결과가 달라지는 경우나 실시간 시스템에는 GC의 사용이 적합하지 않을 수 있다.

<br/>

## 참고

> https://spurdev.tistory.com/10  
> https://coding-factory.tistory.com/829  
> https://hbase.tistory.com/209  
> https://tecoble.techcourse.co.kr/post/2021-08-30-jvm-gc/
