[toc]

# Spring



## Servlet(Optional)

### 개념

- 컨테이너 안에서 돌아가는 자바 프로그램
- 웹 개발에 쓰이는 JEE(혹은 Spring)의 컴포넌트
- 요청을 받아서, 처리하고, 응답한다.
- 반드시 컨테이너에 등록되어야 한다.
  - 컨테이너는 등록된 서블릿의 init() 메서드를 호출해서 인스턴스화 한다.
  - 컨테이너가 요청에 적합한 서블릿 인스턴스를 찾아 service() 메서드를 호출해서 요청 처리를 위임한다. 
    - service() 내부에서 HTTP 요청 타입에 따라 다른 메서드를 호출한다.
      - doGet(), doPost()

### Servlet Container

- Jakarta Servlet specification 구현체
- servlet 호스팅
- 대표 예시
  - Tomcat
- 컨테이너를 실행하면, ServletContext라는 객체를 만든다.
-  ServletContext
  - 서버 또는 컨테이너의 메모리 역할을 한다.
  - 설정 파일(web.xml 등)을 읽어  컴포넌트들을 메모리에 로드한다.
    - 서블릿
    - 필터
      - 실제 서블릿에 접근하기 전에 요청 필터
      - 서블릿이 호출이 끝난 이후 응답 필터
      - 인증, 로깅, 이미지 변환, 압축, 암호화, 토큰, 자원 접근, 이벤트, Mime-type 필터 등
      - FilterChain을 통한 연속된 필터 호출
      - filterChain.doFilter()를 통해 필터 체인의 다음 필터(혹은 서블릿)으로 요청을 넘기거나, 아무것도 하지 않고 넘어갈 수 있음(요청 흐름이 해당 필터에서 끝남)
    - 리스너
      - 서블릿 컨테이너에 이벤트가 일어났을 때 해당 이벤트를 통지받아 처리할 수 있음
      - web.xml 등을 통해 설정
      - 설정이 적용된 컨테이너가 관련 이벤트가 발생했을 때 해당 이벤트 리스너 함수를 호출한다.

- 동작과정
  - 컨테이너가 요청을 받음
  - 요청을 기반으로 HttpServletRequest와 HttpServletResponse 객체를 생성
  - 조건에 매핑되는 servlet 객체의 service()를 호출하면서 매개변수로 위의 두 객체를 전달.
  - service 메서드에서 HTTP method에 맞는 doXXX() 메서드 호출
  - 컨테이너가 service 메서드 호출을 끝내면, 응답을 클라이언트에게 전송하고, HttpServletRequest와 HttpServletResponse 객체를 gc 한다.
  - 상태 기반 HTTP 요청 응답이 필요한 경우 HttpSession을 활용
    - 보통 cookie를 통해 구현
    - JSESSIONID 값을 key로 삼아 필요한 데이터들을 저장한다.
  - 

## Spring 기본

### Spring vs Spring MVC vs Spring Boot

- Spring
  - 엔터프라이즈 수준의 자바 애플리케이션을 쉽게 개발하기 위한 프레임워크
  - IoC(DI)
    - 실행 흐름을 프레임워크 사용자가 아닌 프레임워크가 쥐고 있음
    - 개발자의 소스 코드를 호출하는 책임은 개발자 코드가 아닌 프레임워크
    - 
  -  AOP
  - 기타 기능(ApplicationContext)
    - Resource 관리
    - 이벤트 처리
    - 메시징
- Spring MVC
  - Servlet API 위에서 만들어진 웹 프레임워크
- Spring Boot
  - Conversion over Configuration
    - 직접 하나 하나 설정하기 보다는 기본 값을 설정하고, 필요할 때만 설정을 바꾸자.

### MVC1 vs MVC2

- MVC1(Model 1)

  - 뷰와 컨트롤러가 결합되어 있는 MVC 패턴
  - 요청이 들어오면 요청에 매핑되는 JSP page가 비즈니스 로직을 처리해서 모델을 구성하고, 그 모델을 바탕으로 페이지를 동적으로 구현
  - 사실상 MVC가 아님

- MVC 2

  - Controller 서블릿에서 요청을 받아서 모델을 구성하는 책임을 갖고 적합한 View 처리기를 찾아서 모델을 view로 렌더링하는 것을 위임한다.

  - View 계층에서는 컨트롤러에서 건네준 모델을 받아서 view로 만드는 책임을 갖는다.

    

### dispatcher servlet

- front controller pattern 구현
  - 특정 endpoint에 대한 모든 요청을 받아서, 그 요청을 파싱하여 적합한 Controller에게 건내준다.
    - 이 때 등록된 ApplicationContext를 탐색한다.
  - 컨트롤러를 선정하는 방식 구현을 HandlerMapper에게 위임한다.
  - HandlerAdapter는 요청 객체에 있는 데이터를 컨트롤러 Handler함수에 맞는 매개 변수로 변환하여 handler를 호출한다.
- 실질적인 비즈니스 로직 처리는 RootWebApplicationContext에 있는 Component들
- web.xml 혹은 WebApplicationInitailizer 구현체에서 등록한다.
  - ServletContainer가 탐색해서 실행한다.
- 각각의 DispatcherServlet은 application Context를 갖고있다.

## IoC

- 직접 우리가 실행 흐름을 제어하는 코드를 작성하는 것이 아닌, 콜백(handler, controller) 중심으로 함수 호출을 구현
- 프레임워크에서 주어진 인터페이스들을 개발자들이 '구현'하고, 프레임워크가 해당 구현체들을 실행한다.

## DI

- IoC의 종류 중 하나
- 의존성을 생성자/setter 등을 통해 '외부로부터' 주입받는다.
  - 스프링에서는 이 외부가 IoC Container

## Bean, Component

- `@Component` `@Service` `@Controller`

## VO vs DTO vs DAO

### VO

- 값을 저장하는 객체
- 타입은 객체이나 값의 성질을 갖는다.
  - 동일성(객체 주소) 비교가 아닌 동등성(특정 필드 값) 비교
  - 불변성

### DTO

* 다양한 계층의 소프트웨어 시스템들 간의 데이터 전송을 위해 이용는 디자인 패턴
* 데이터에 대한 접근 메서드(보관) 외에는 추가적인 비즈니스 로직이 없다.
* 보통 네트워크를 통한 데이터 전송을 효율적으로 하기 위해 사용
  * 따라서 java의 경우 serializable을 구현할 때도 있음

### DAO

- 데이터베이스와 같은 영속성 메커니즘에 접근할 수 있는 인터페이스를 제공하는 디자인 패턴
- 영속성 메커니즘의 내부 구현을 추상화한 데이터 관련 연산 API 제공
- 비즈니스 로직을 가진 애플리케이션 계층의 코드가 특정 db와 결합되지 않는 코드
- 데이터베이스의 데이터에 접근하는 책임을 가진 객체
- DB CRUD 구현