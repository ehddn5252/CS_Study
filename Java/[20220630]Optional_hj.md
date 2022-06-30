# Optional<T>

지네릭 클래스로 “T타입의 객체"를 감싸는 래퍼 클래스이다. 그래서 Optional 타입의 객체에는 모든 타입의 참조변수를 담을 수 있다.

최종연산의 결과를 그냥 반환하는게 아니라, Optional 객체에 담아서 반환하는 것이다.

Optional에 정의된 메서드를 통해 반환된 결과가 null인지 체크하기 위한 if문 없이도 NullPointerException 이 발생하지 않는 간결한 코드 작성이 가능하다.

### Optional 객체 생성

**of() 또는 ofNullable()** 을 사용한다.

참조변수의 값이 null일 가능성이 있으면, of() 대신 ofNullable()을 사용한다.

```java
String str = "abc";
Optional<String> optVal = Optional.of(str);
Optional<String> optVal = Optional.of("abc");

Optional<String> optVal = Optional.of(null); // NullPointException 발생
Optional<String> optVal = Optional.ofNullable(null); // ok
```

참조변수를 기본값으로 초기화 할때는 empty()를 사용한다.
null로 초기화하는것이 가능하지만, empty()로 초기화하는것이 바람지갛다.

```java
Optional<String> optVal = null; // null로 초기화
Optional<String> optVal = Optional.<String>empty(); // 빈 객체로 초기화
```

### Optional 객체 값 가져오기

Optional 객체의 값을 가져올때 get()을 사용하는데, 값이 null이면 NoSuchElementException이 발생하므로, 이를 대비해 **orElse()**로 대체값을 지정할 수 있다.

```java
Optional<String> optVal = Optional.of("abc");
String str1 = optVal.get(); // null이면 예외발생
String str2 = optVal.orElse(""); // null이면 ""반환
```

orElse()의 변형

**orElseGet()** - null을 대체할 값을 반환하는 람다식을 지정할 수 있다

**orElseThrow()** - null일때 지정된 예외를 발생시킨다

```java
String str3 = optVal2.orElseGet(() -> new String());
String str3 = optVal2.orElseGet(String::new); // 위와 동일

String str4 = optVal2.orElseThrow(NullPointerException::new); // null이면 예외발생
```

Stream처럼 Optional 객체에도 filter(), map(), flatMap()을 사용할 수 있다. 만일 Optional 객체의 값이 null이면, 이 메서드들은 아무일도 하지않는다.

**isPresent()** - Optional객체의 값이 null이면 false를, 아니면 true를 반환한다.
