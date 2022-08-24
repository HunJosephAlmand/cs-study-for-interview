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
