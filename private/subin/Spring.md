---

mindmap-plugin: basic

---

# Spring

## Spring vs Spring Boot

## Bean, Component

## Spring Security

## Spring JPA

## Spring Web MVC
- 서블릿을 low level로 개발하지 않아도 된다
- 구성요소
   - Dispatcher Servlet
      - 애플리케이션으로 들어오는 모든 Reqest를 받는 서블릿 구현체
   - Handler Mapping
      - 클라이언트의 요청을 바탕으로 어떤 Handler(Controller 메소드)를 실행할지 결정
   - Handler Adapter
      - 컨트롤러 실행
   - Model
      - Controller에서 View로 넘겨줄 객체가 저장되는 곳
   - View Resolver
      - view name을 View Resolver에게 전달하여 View 객체를 얻는다
   - View
      - 뷰에 화면 표시 의뢰
      - ex) JSP, 타임리프
   - Controller
- 흐름
   - 애플리케이션을 실행하면 WAS가 처음으로 구동되어 Servlet Container 구동
   - Servlet Container는 ContextLoaderListener 객체를 자동으로 메모리에 생성
   - ContextLoaderListener은 Spring Container(== root Container)를 구동하고 CompoenetScan으로 DAO,Service,Bean들을 찾고 메모리에 등록
   - 다음으로 요청이 들어오면 Servlet Container가 DispatcherServlet 객체를 생성한다
      (첫 요청에 대하여 최초로 한번만 생성)
   - init()메소드가 호출 된 후 Spring Container를 구동하고 Service() 메소드에 의하여 필요한 Controller를 찾아 실행
- filter & intercepter
   - filter
   - intercepter

## AOP
- 관점 지향 프로그래밍 (Aspect-oriented Programming)
- AOP는 부가 기능을 핵심 기능으로부터 분리하기 위해 등장한 기술
   - 흩어진 관심사(Crosscutting Concerns)를 묶어서 모듈화 한 것
   - 모듈화
      - 어떤 공통된 로직이나 기능을 하나의 단위로 묶는 것
- AOP 구현방법
   - 코드에 공통 기능을 삽입
      - 컴파일 시점에 특정 코드 삽입
         - 컴파일 A.java ---(AOP)--> A.class (AspectJ)
      - 클래스 로딩 시점 바이트 코드에 삽입
         - A.java -> A.class ---(AOP)--> 메모리
      - 런타임에 프록시 객체를 생성해 삽입
         - 기존 코드를 건드리지 않고 새 기능을 추가
         - 스프링 AOP가 사용하는 방법
         - 타겟을 감싸는 프록시는 실행시간(Runtime)에 생성
- 용어
   - Aspect
      - Advice + Pointcut
      - Advice
         - 어드바이스는 타겟에 제공할 부가기능을 담고 있는 모듈
         - 어떤 일을 해야할 지에 대한 것
            - 해야할 일들에 대한 정보를 가지고 있다
      - Point Cut
   - Join point
      - 어드바이스가 적용될 수 있는 위치
         - 메서드 실행 시점
      - Advice가 적용될 위치, 끼어들 수 있는 지점.
         ex 생성자 호출 직전, 생성자 호출 시, 필드에 접근하기 전, 필드에서 값을 가져갔을 때 등
   - Point Cut
      - 어디에 적용해야 하는지에 대한 정보
   - target
      - 핵심 기능을 담고 있는 모듈로 타겟은 부가기능을 부여할 대상
   - 위빙(Weaving)
      - 위빙은 포인트컷에 의해서 결정된 타겟의 조인 포인트에 부가기능(어드바이스)를 삽입하는 과정
- AOP 구현체
   - AspectJ
      - Spring AOP는 메서드 조인 포인트만 지원
         - 핵심기능(타겟)의 메서드가 호출되는 런타임 시점에만 부가기능(어드바이스)을 적용할 수 있다
      - 반면에 AspectJ 같은 고급 AOP 프레임워크를 사용하면
         객체의 생성, 필드값의 조회와 조작,
         static 메서드 호출 및 초기화 등의 다양한 작업에 부가기능을 적용 할 수 있다
   - 스프링 AOP
      - 프록시 기반의 AOP 구현체
      - 스프링 빈에만 AOP를 적용할 수 있다
      - 중복코드, 프록시 클래스 작성의 번거로움, 객체 간 관계 복잡도 증가 등
         를 해결하기 위한 솔루션을 제공하는 것이 목적
      - @EnableAspectJAutoProxy(proxyTargetClass = true)
         - Spring boot 기본값은 true
         - Spring의 기본값은 false
            - 프록시를 적용하기 위해서 반드시 인터페이스를 생성해야 함
            - 구체 클래스로는 빈을 주입받을 수 없고, 반드시 인터페이스로만 주입받아야 함
      - spring-boot-starter-aop 의존성 추가시 위 어노테이션 자동설정됨

## 스프링 삼각형
- Sub title