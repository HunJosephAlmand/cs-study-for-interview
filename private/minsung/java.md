[TOC]

# JDK, JRE, JVM

## JRE

JVM + Java 플랫폼 핵심 클래스 + 자바 플랫폼 라이브러리

자바 애플리케이션을 구동시키기 위한 최소한의 요구 사항

## JDK

JRE + 컴파일러(javac) + 패키징(jar) + 배포

## JVM

- 추상 컴퓨팅 머신

  - 자바 bytecode -> 설치된 운영 체제의 명령어

- 특정 구현 기술, 호스트 컴퓨터의 하드웨어, 운영체제 등에 종속되지 않는다.

  - JVM이 받아들이는 파일은 .class 밖에 없음
  - class 파일에는 bytecode와 symbol table 등이 들어 있음
  - bytecode가 설치된 시스템의 명령어로 전환되는 방식은 JVM을 구현한 프로그램에 따라 다르다.

## 컴파일 과정

컴파일 타임 환경

- 자바 클래스 파일(.java) -> javac -> 자바 바이트 코드(.class)
  - .class : 컴퓨터가 아닌, JVM만 이해할 수 있는 바이트 코드.

런타임 환경

- 클래스로더(동적 로딩) -> 인터프리터, JIT 컴파일러 -> Runtime Data Area(Heap, Stack, Method(static, class), PC register, Native Method Stack)
- Runtime Data Area
  - JVM의 메모리 영역

### 클래스 로더

런타임에 클래스를 동적으로 JVM의 메모리에 로딩하는 책임을 가진다. JRE의 일부

- 검증

  - 자바 언어 명세 및 JVM 명세에 맞는지 체크

- 준비

  - 클래스가 필요로 하는 메모리 할당

- 분석

  - 클래스 상수 풀 내부의 심볼릭 레퍼런스를 다이렉트 레퍼런스로 변경

- 초기화

  - 클래스 변수를 적절한 값으로 초기화

- **클래스 로더로 인해 JVM 클래스 파일과 파일 시스템을 알 필요가 없게 된다.**
  - 클래스 몸체에 접근할 수 있는 문자열 namespace를 제공한다.
  
- 클래스 로딩 시점

  - 동적으로, 애플리케이션이 '필요로 할 때' 클래스 로더에 의해 로딩된다.
  - new로 클래스 인스턴스가 생성됐을 때
  - 클래스에 대한 정적 레퍼런스 코드가 실행됐을 때

- Bootstrap Class Loader

  - 모든 ClassLoader(이들 또한 class다)들을 로딩하는 역할
  - JVM의 core에 위치하고 있으며, native code로 작성됐다.

- System Class Laoder

  - 애플리케이션 수준의 class들을 로딩하는 역할
  - classpath 환경 변수, -classpath command line option에서 발견된 classfile들을 로딩하는 역할

실행 엔진

인터프리터 : 바이트 코드 명령어를 **하나씩** 해석하고 실행

JIT 컴파일러 : 반복되는 바이트코드를 캐싱한 다음, 해당 바이트 코드를 컴파일할 때 캐싱된 바이트코드르 불러온다.

### JVM 메모리 구조

- garbage-collected heap

  - 런타임 객체, 배열 저장
  - 가비지 컬렉팅 대상

- Class(method) area
  - 클래스 별 구성된 자료 구조
  - 코드, 상수, 기타 클래스 데이터 보관
- call stack
  - thread들이 각자 소유
  - frame 기반
- Program Register Counter
  - JVM instruction이 실행되는 위치
- Native Method Stack
- Execution Engine
- Java Native Interface(JNI)

### Java Garbage Collection

- 힙 메모리에 있는 객체들의 사용 유무를 확인하고, 사용되지 않는 객체들을 제거하는 절차
- 사용되는 객체
  - 실행되고 있는 프로그램의 일부에서 해당 객체에 대한 포인터를 보유하고 있음.
- 사용되지 않는 객체
  - 프로그램의 어느 영역에서도 참조하고 있는 포인터가 존재하지 않음.
- 진행 과정
  1. Marking
     - 메모리를 사용되는 영역과 사용되지 않는 영역으로 분리
  2. Normal Deletion
     - 참조되지 않는 객체들을 제거한다.
     - 제거된 메모리 공간에 대한 참조를 리스트에 보관한다.
     - memory allocator는 새로운 객체를 메모리 공간에 할당할 때, 해당 리스트를 참조한다.
  3. Deletion with Compacting(Optional)
     - 객체들을 삭제한 다음 나머지 객체들을 특정 공간에 순서대로 배치시킨다.
     - memory allocator는 빈 공간의 시작 부분에 대한 주소를 참조로 갖고, 새로운 객체가 생성될 때마다, 순차적으로 공간에 할당시킨다.
- JVM Generation
  - GC 과정에서 항상 모든 객체들을 탐색하기 때문에 성능 문제가 발생.
  - 애플리케이션에서 생성된 대부분의 객체의 수명이 짧다는 사실에 근거하여 **heap을 generation 기반으로 분리를 시킴**
  - 종류
    - Young
      - 새로운 객체들이 배치
      - 꽉 찰 때마다 minor garbage collection이 수행된다.
      - 대부분의 객체들이 빠르게 제거되고, 그 중 오래 살아남는 객체들은 Old Generation으로 이동된다.
      - minor garbage collection이 수행될 떄마다 애플리케이션의 모든 스레드 동작이 중단된다.(Stop The World Event)
    - Old
      - 오래 살아남는 객체들을 보관
      - 꽉 찼을 때, major garbage collection이 작동된다.
      - minor와 마찬가지로 GC 될 때 모든 스레드가 중단된다. 다만 Young보다 탐색하는 객체의 수가 많기 때문에, 빠른 반응이 요구되는 애플리케이션에서는 자주 일어나지 않게 해야 한다
    - Permanent
      - 클래스와 메서드의 메타데이터들 보관
      - runtime에 JVM에 의해 채워진다.
      - Java SE library classes, methods

## JAVA 11

- 모듈(Modularity)
- var keyword
  - 람다식에서 var 사용 가능
- String 메서드
  - isBlank()
  - lines()
  - repeat(int n)
  - stripLeading()
- 파일 관련 메서드 추가

  - Files.writeString()
  - Files.readString()
  - Files.isSameFile()

- Arrays.asList()
- java 소스 파일을 컴파일하지 않고 직접 실행 가능

## Java 8

- 인터페이스 default 메서드, static 메서드

- 메서드 참조

  - 정적 메서드 참조
    - `메서드보유클래스::메서드이름`
  - 인스턴스 메서드 참조
    - `메서드보유인스턴스::메서드이름`
  - 특정 타입의 인스턴스 메서드 참조
    - `메서드보유타입명::메서드이름`
  - 생성자 참조
    - `클래스이름::new`

- Optional

## Java 17

- Shield Class

### JDK, JRE, JVM 질문

- 컴파일 언어와 인터프리터 언어의 차이점?
- 개발자가 작성하고 컴파일한 클래스 파일은 어느 시점에 JVM 메모리에 로드 되는가?
- 일반적으로 가비지 컬렉터는 힙에 있는 모든 객체들을 확인하는 절차를 거친다. 이것은 성능 문제를 유발할 수 있는데, 이것을 극복하기위해 자바는 어떤 방법을 사용하고 있는가?

# Java 기본

## 접근제어자

* 컴포넌트(패키지, 클래스) 간 결합도를 낮춘다.
* 내부 구현 사항(메서드, 데이터)을 외부에 숨기기 위해서

### public

* 모든 클래스에서 접근 가능

### protected

* 같은 파일
* 같은 패키지
* 상속 받은 클래스에서 접근 가능

### package private(default)

* 같은 파일
* 같은 패키지

### private

* 같은 파일 안에 있는 클래스만 접근 가능



### 클래스, 객체, 인스턴스 차이

|             | 클래스                                            | 객체                                                  | 인스턴스                          |
| ----------- | ------------------------------------------------- | ----------------------------------------------------- | --------------------------------- |
| 메모리 위치 | 메서드 영역                                       | 힙 영역                                               | 힙 영역                           |
| 기능        | 객체의 공통적인 것들을 추상화해서 모아놓은 청사진 | 클래스를 기반으로 생성되어 메모리에 상주하는 데이터들 | 특정 객체를 참조할 수 있는 참조값 |
| 구분        | 불가능                                            | (같은 타입일 경우)불가능                              | 식별자로 구분                     |



### Overloading vs Overriding

#### Overloading

* 같은 이름을 가진 메서드가 넘겨 받는 매개 변수의 종류와 개수에 따라 다른 동작을 할 수 있게 해주는 기능
* 이름은 같으나 매개 변수가 다른 메서드들을 개별로 등록할 수 있다.

#### Overriding

* 같은 시그니처를 가진 메서드를 하위 타입에서 구현 했을 경우, 하위 타입의 메서드만 호출하도록 해주는 기능.

### Primitive type vs Reference type

* Primitive type
  * 변수에 주소값이 아닌 실제 값 할당
  * int, long, short, byte, char, double, float
  * == 연산을 할 경우 실제 값을 비교한다.
* Reference type
  * 변수에 실제 객체가 저장된 힙 주소값을 갖고 있음
  * 참조하고 있는 변수가 없을 경우 garbage collection 대상이 된다.
  *  == 연산을 할 경우 주소값을 비교한다(동일성 비교)

### Call by Reference vs Call by Value

* 메서드의 매개변수로 참조 타입 변수를 넘길 경우, 메서드의 매개변수와 참조 타입 변수가 가리키는 객체가 동일하다.
  * 주소값을 넘긴다.
  * 즉 메서드에서 객체의 상태를 바꾸는 동작을 수행할 경우, 해당 객체를 사용하는 다른 코드들에도 영향을 주는 side effect가 발생한다.
* 원시 타입 변수를 매개변수로 사용할 경우, 매개 변수에는 실제 값이 들어온다.
  * 기본 타입 매개 변수는 메서드 바깥의 영역에 영향을 주지 않는다. 매개변수로 참조 타입 변수를 넣어서 넘겨줬을 경우, 그 변수값 주소가 아닌 변수값 안에 있는 값을 복사해서 넘겨주기 때문
  * 해당 매개 변수 관련 수정 코드를 작성해도 side effect 없음

### Wrapper Class

* 원시 타입 데이터를 감싼 객체

* 값에 관한 편의 메서드를 따로 제공할 수 있다.(최솟값, 최댓값)

* 컬렉션 프레임워크, 제네릭 등에서 원시 타입 데이터들을 사용하고 싶을 때 사용(타입 변수에는 원시 타입이 올 수 없음)

* 불변 객체

* autoboxing

  * 기본 타입을 매칭되는 원시 타입으로 바꿔주는 기능
  * 컴파일 단계에서 이루어진다.(소스 코드를 바꾼다)
    * `Integer a = 10; => Integer a = Integer.valueOf(10);`

  

### interface vs abstract class

* 추상 클래스는 인스턴스 필드를 가질 수 있으나 interface는 그렇지 못하다. 

  * 인터페이스는 정적 필드만 가질 수 있다. (public static final)
  * 클래스의 구현이 최 우선순이
  * hashCode()  같은 것들을 default로 재정의 할 수 없다.

* 인터페이스는 공개 API의 기본 동작을 정의한 디폴트 메서드를 사용할 수 있다. 인터페이스 구현체에서 해당 메서드를 override할 수 있다.

  * 디폴트 메서드는 final로 선언할 수 없다.

* 기본적으로 인터페이스에 선언되는 메서드는 public 추상메서드다. 

* 추상 메서드는 하위 클래스들이 반드시 재정의해야 한다.

  * final을 선언할 경우 재정의할 수 없다.

* 둘 다 인스턴스화 될 수 없다.

* 클래스는 여러 개의 인터페이스를 구현할 수 있으나, 상위 클래스는 하나만 확장 가능하다.

* 인터페이스 : 믹스인 정의에 적합하다.

  * Serializable, Comparable
  * 다이아몬드 문제
    * 두 클래스가 상속받았을 때 어떤 것이 우선되어야 하는가 문제
    * 자바에서는 다중 상속을 막아 놓았다.

* 템플릿 제공용

  * 공통 필드
  * 공통 메서드

  


### Checked Exception vs UnChecked Exception

* Checked Exception : 컴파일러가 예외 처리 코드 작성을 강제한다. try-catch로 처리하거나, 메서드 시그니처에 해당 예외를 throw한다고 작성해야 한다.
  * 실행 흐름을 복구 시킬 수 있다는 것을 가정
  * 예외 처리 코드에는 실행 흐름을 다시 복구시키도록 시스템적으로 강제

* Unchecked Exception : RuntimeException 상속, 예외 처리를 컴파일러가 강제하지 않는다.
* 예외 회피(throw), 예외 복구(다른 프로그램 실행 흐름으로 번환), 예외 번환(예외 번역)

### static

* 해당 멤버가 인스턴스가 아닌 타입 자체에 속한다는 것을 선언
  * 객체 생성 없이 사용 가능
* 모든 인스턴스/다른 클래스가 공유할 수 있음(접근 제어자에 따라)
  * 클래스의 이름으로 접근
  * 인스턴스 변수명에서도 접근할 수 있으나 지양할 것
* static field
  * 애플리케이션에서 단 하나의 필드만 생성
* static method
  * 상속될 수 없음
  * this, super 사용 불가(인스턴스 메서드가아니기 떄문)
  * 인스턴스 변수, 메서드 직접 접근 불가
* static block
  * 클래스가 JVM의 메모리에 로드되었을 때 한 번만 동작하는 코드
  * 정적 초기화 블록이라고도 부른다.
  * 정적 멤버 초기화 코드가 한 줄 이상일 때 사용한다.
  * 정적 멤버 초기화 로직에서 예외 처리가 필요할 때
  * **정적 필드 할당과 정적 블록은 작성한 순서대로 코드가 실행된다.***
* static class
  * 정적 중첩 클래스라고도 불림(cf : 내부 클래스 : 정적 클래스가 아닌 중첩 클래스)
  * 바깥 클래스의 정적 멤버만 접근할 수 있음
    * 내부 클래스 : private 포함 모든 멤버 접근 가능
      * 내부 클래스가 바깥 인스턴스를 참조하고 있으므로 gc되지 않는다.

### final

* 필드에 선언했을 경우
  * 첫 번째 할당 이후 데이터 변경 불가능
  * 인스턴스 필드의 경우 생성자 호출이 끝나는 시점에는 반드시 할당되어 있어야 한다.
  * 정적 필드의 경우 선언과 동시에 초기화하거나, static block에서 값을 할당해야 한다.
* 지역 변수에 선언했을 경우에는 선언과 동시에 값을 할당시켜야 한다.
* 메서드에 선언했을 경우
  * override 불가능
* 클래스에 선언했을 경우
  * 상속받는 하위 클래스(스프링에서 만들어주는 프록시 객체 등) 작성 불가능





## Generic

* type safe한 코드를 작성할 수 있게 된다.
* 해당 클래스, 메서드에서 다루는 타입이 object 일 경우, 내부 타입이 어떤 것이 정확하게 알 수 있는 방법은 없음.
  * java.util.Collection
* 클래스, 메서드가 다루는 타입을 정확하게 명시해서, 컴파일러가 타입에 안전한 코드인지 확인할 수 있게 한다.

#### Bounded Generic

* 타입 변수의 범위를 제한할 수 있다.

* Upper bound

  * T extends supertype
  * supertype이나 supertype을 상속받는 타입만 매개변수로 사용할 수 있다.

* Wildcard

  * ?로 표기한다.

  * 제네릭으로 사용할 수 있다.

  * 왜 사용하지?

  * Object는 모든 타입의 상위 타입이다. 하지만 Object 타입을 제네릭 변수로 사용하는 타입은 다른 타입을 제네릭 변수로 사용하는 타입의 상위 클래스가 아니다.

    * 공변성 X

    * 제네릭 변수인 타입의 서브 타입을 가진 클래스 또한 포함시키기 위해

    * ```java
      public static void paintAllBuildings(List<? extends Building> buildings) // 그냥 Building 변수를 사용하면 안 된다.
      ```

    * Upper bound wildcard

      * ? extends T
      * T 타입의 superclass만 제네릭 변수로 올 수 있음

    * Lower bound wildcard

      * ? super T
      * T타입의 상위 타입만 제네릭 변수로 올 수있음

#### Type Erasure

* 런타임 환경에서는 제네릭 관련 overhead가 발생하지 않는다.
  * 컴파일 과정에서 제네릭 관련 코드들을 삭제하기 때문
  * 타입 변수가 unbounded일 경우 Object로
  * 타입 변수가 bounded일 경우 해당 bounded type으로

* List<T> -> List
* public <T extends Building> void genericMethod(T t) 
  * -> pulbic void genericMethod(Building T)



## Stream

* 순서를 가진 원소들의 모임
  * 순서/병렬 연산을 지원한다.
* parallelStream()으로 멀티 스레드 연산을 할 수 있다.

## Lambda

* Functional Interface 구현체
  * 이름이 존재하지 않는다.
  * 매개 변수를 받아서 연산을 수행하거나 값을 반환시키는 코드로 표현
* 다만 성능 향상을 위해 익명 클래스로 컴파일 되지 않음.
  * lambda 표현식에 있는 this(해당 메서드를 가진 클래스)와 익명 클래스의 this(익명 클래스)는 다르다.



## Reflection

* 실행되는 자바 프로그램의 메타데이터를 프로그래밍적으로 활용할 수 있게 하는 기능
  * 클래스 이름
  * 메서드 이름
  * 필드 이름
  * 붙어 있는 애너테이션
* 프레임워크에서 적극적으로 사용
  * 특정 annotation이 붙은 클래스만 실행한다(@Test)
* 접근 제어자로 인해 접근이 불가능한 필드에 접근할 수 있음



## Dynamic Proxy

* 프록시 기존에 존재하는 함수에 부가적인 기능을 추가해주는 클래스
* 모든 메서드 호출을 하나의 핸들러(invoke())에 모아놓는다.
* dynamic proxy  : 하나의 클래스의 하나의 메서드로 여러 클래스의 여러 메서드들에 추가적인 기능을 부가할 수 있는 클래스
  * InvocationHandler 인터페이스 구현체로 프록시 클래스 구현
  * 공개 메서드가 하나이기 때문에 lambda 표현식으로도 포현할 수 있다.
* JDK에 포함되어 있다.
* Proxy.newProxyInstance()로 프록시 클래스 생성 가능
