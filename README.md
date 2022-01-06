# 웹 애플리케이션 이해
## 웹 서버
- HTTP 기반으로 동작
- 정적 리소스 제공
- 정적 파일: HTML, CSS, JS, 이미지, 영상
- 예) NGINX, APACHE

## 웹 애플리케이션 서버 (WAS)
- HTTP 기반으로 동작
- 웹 서버 기능 포함
- 프로그램 코드를 실행하여 애플리케이션 로직 수행
- 예) Tomcat, Jetty

## 웹 시스템 구성
### WAS-DB
- WAS가 너무 많은 역할 -> 서버 과부하 우려
- WAS 장애시 오류 화면 노출 불가능

### WEB-WAS-DB
- 정적 리소스는 웹 서버가 처리
- 동적인 처리는 WAS가 처리
- 리소스 관리가 효율적
    - WEB, WAS 각각 요청이 많은 서버를 증설
- 웹 서버는 잘 죽지 않음
- WAS, DB 장애시 WEB이 오류 화면 제공

## 서블릿

### 서블릿 컨테이너 생성
1. 스프링부트를 실행하면 스프링 부트가 내장 톰켓 서버를 띄움
2. 톰캣 서버는 내부에 서블릿 컨테이너를 가지고 있고, 서블릿 컨테이너를 통해 서블릿을 생성

### HTTP 요청 응답
1. WAS는 리퀘스트, 리스폰스 객체를 만들어서 서블릿을 호출
2. 호출한 서블릿에 생성한 리퀘스트, 리스폰스를 넘겨줌
3. 서블릿이 종료되고 나가면서 WAS 서버가 response를 가지고 http 메시지를 만들어 반환

### 서블릿 컨테이너
- 서블릿을 지원하는 WAS
- 서블릿 생명주기를 관리
- 서블릿 객체는 싱글톤으로 관리
    - 객체를 미리 생성하여 재활용
- 멀티 스레드 지원

## 멀티 스레드
### 요청마다 스레드 생성
- 장점
    - 동시 요청 처리 가능
    - 리소스를 활용도가 높음
    - 한 스레드가 지연되어도 나머지 스레드는 동작
- 단점
    - 스레드 생성 비용은 비쌈 -> 응답 속도 저하
    - 컨텍스트 스위칭
    - 제한 없이 생성하여 임계점을 넘으면 서버가 죽음

### 스레드풀
- 요청마다 스레드 생성하는 것을 보완
- 스레드를 스레드 풀에 보관하고 관리
- 스레드의 최대치를 관리
- 동작
    1. 스레드가 필요하면 스레드풀의 스레드를 사용
    2. 사용이 끝나면 스레드풀에 스레드 반납
    3. 스레드가 모두 사용중이면 대기
- 장점
    - 스레드 생성 비용 절약
    - 스레드를 생성하지 않아 응답 시간이 빠름
    - 많은 요청에 대해서 서버가 죽지 않고 안전하게 동작

#### max thread
- 최대 스레드수
- 너무 낮으면 응답 지연
- 너무 높으면 서버 다운

## SSR & CSR
- SSR: 서버 사이드 렌더링
    - 서버에서 HTML 최종 결과를 만들어서 전달
    - 정적인 화면에 사용
- CSR: 클라이언트 사이트 렌더링
    - 자바스크립트로 HTML 결과를 동적으로 생성
    - 동적인 화면에 사용
    - 부분 변경이 가능

### SSR 과정
1. HTML 요청
2. DB 조회
3. 서버에서 HTML 생성

### CSR 과정
1. HTML 요청
2. JS 요청
3. HTTP API - 데이터 요청
4. JS로 HTML 렌더링


# 서블릿
> 자바를 사용하여 웹 페이지를 동적으로 생성하는 서버측 프로그램

## 서블릿 기본
### `@ServletComponentScan`
- 서블릿을 직접 등록할 때 사용

### `@WebServlet`
- `name`: 서블릿 이름
- `urlPatterns`: URL 매핑
    - URL이 호출되면 `protected void service(HttpServletRequest request, HttpServletResponse response)` 메서드 실행

### 메시지 로그 확인
``` java
logging.level.org.apache.coyote.http11=debug
```
`application.properties`에 위의 설정 추가

## 서블릿 컨테이너 동작 방식
1. 내장 톰캣 서버 생성
2. 내장 톰캣 서버가 `@WebServlet`에 해당하는 서블릿을 서블릿 컨테이너에 생성

## `HttpServletRequest`
- 서블릿이 파싱한 HTTP 요청 메시지를 `HttpServletRequest` 메시지에 담아서 제공
- 해당 HTTP 요청 메시지 시작부터 끝날 때까지 유지되는 임시 저장소 기능
    - 저장: `request.setAttribute(name, value)`
    - 조회: `request.getAttribute(name)`
- 세션 관리 기능
    - `request.getSession(create: true)`

## HTTP 요청 데이터

### 클라이언트에서 서버로 데이터를 전달하는 방법
- GET - 쿼리 파라미터
    - /url?data=1
    - 메시지 바디 없이 쿼리 파라미터로 데이터를 전달
    - 검색, 필터, 페이징 등에서 사용
- POST - HTML Form
    - content-type: application/x-www-form-urlencoded
    - 메시지 바디에 쿼리 파라미터 형식으로 전달
- HTTP message body
    - HTTP API에서 주로 사용

### GET 쿼리 파라미터
- URL에서 `?` 뒤에 오는 것이 쿼리 파라미터
- `이름 = 값` 형태로 추가
- 여러개의 파라미터를 보낼 때는 `&`를 사용
- `request.getParameter("이름")`의 형식으로 데이터를 조회 -> 같은 이름의 파라미터가 여러 개 있을 때는 불가능, 첫번째 값만 조회
    - 같은 이름의 중복되는 파라미터를 조회할 때는 `request.getParameterValues("이름")` 사용
- 모든 파라미터를 조회할 때는 다음과 같은 방식 사용
``` java
Enumeration<String> parameterNames = request.getParameterNames(); //파라미터 모두 조회
Map<String, String[]> parameterMap = request.getParameterMap(); //파라미터를 Map으로 조회
```

### POST Form
- content-type: application/x-www-form-urlencoded
    - content-type은 메시지 바디가 있을 때 사용
- 메시지 바디에 쿼리 파라미터 방식으로 데이터 전달
- GET과 같은 방식으로 조회

### JSON
``` java
ServletInputStream inputStream = request.getInputStream();
String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
private ObjectMapper objectMapper = new ObjectMapper();
HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
```
- SpringMVC에서 제공하는 Jackson 라이브러리 `ObjectMapper`을 사용하여 JSON 파싱

## HttpServletResponse
- HTTP 응답 메시지 생성
    - HTTP 응답 코드 지정
    - 헤더 생성
    - 바디 생성
- 편의 기능 제공
    - Content-Type, 쿠키, Redirect
- 사용 방법
    - `response.setXxx()` OR `response.addXxx()` 등의 메서드 사용

> `application/json`은 `utf-8` 형식을 사용하기 때문에, `charset=utf-8` 파라미터를 추가할 필요 없음

<br /><br />

# JSP & MVC 패턴
> 동적인 HTML 문서를 만들 수 있는 템플릿 엔진

## JSP 사용 방법
- `build.gradle`에 라이브러리 추가
``` gradle
//JSP 추가 시작
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'javax.servlet:jstl'
//JSP 추가 끝
```
- `<%@ page contentType="text/html;charset=UTF-8" language="java" %>`
    - JSP 파일의 첫 줄에 추가
    - JSP 문서라는 의미
- 동적인 변경이 필요한 부분에 자바 코드를 적용
    - `<%`와 `%>` 안에 자바 코드를 작성
        - `<%@ page import="import할 대상"%>`: java import에 사용
        - `<% %>`: 자바 코드 입력
        - `<%= %>`: 자바 코드 출력

### JSP의 한계
- 자바 코드가 JSP 파일에 너무 많이 있으면, JSP가 너무 많은 역할을 하게 됨 -> 유지 보수가 어려움
> JSP는 화면(View)을 그리고 비즈니스 로직은 다른 곳에서 처리할 수 있도록 변경

## MVC 패턴
- 너무 많은 역할
    - 하나의 파일에서 해야할 작업이 많으면 유지보수가 어려워짐
- 변경 라이프 사이클
    - 비즈니스 로직을 처리하는 것과 UI를 수정하는 일은 다르게 발생할 가능성이 높음
    - 두 작업은 서로에게 영향을 주지 않음
- 기능 특화
    - 뷰 템플릿은 화면 렌더링 업무만 담당하는 것이 효과적

### Model View Controller
- 컨트롤러
    - HTTP 요청을 받아 파라미터를 검증
    - 비즈니스 로직 실행
    - 뷰에 전달할 데이터를 모델에 담음
- 모델
    - 뷰에 전달할 데이터 저장
    - 뷰가 렌더링 하는 일에 집중할 수 있게 됨
- 뷰
    - 모델의 데이터를 사용하여 화면을 렌더링
> 컨트롤러에 비즈니스 로직을 두면 너무 많은 역할을 하게 됨. 비즈니스 로직은 Service 계층에서 처리할 수 있도록 하고 컨트롤러는 이 서비스만 호출.

### 적용
- MVC
    - M: `HttpServletRequest` 객체
    - V: JSP
    - C: 서블릿
- `dispatcher.forward()`
    - 다른 서블릿이나 JSP로 이동할 수 있는 기능
    - 서버 내부에서 다시 호출
- `/WEB-INF`
    - 외부에서 직접 JSP 호출 불가능
    - 컨트롤러로 JSP를 호출

``` java
// Controller
String viewPath = "/WEB-INF/views/file.jsp";
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

> redirect vs forward  
> redirect는 다른 경로로 변경 되는 것. forward는 서버 내부의 호출이라 경로가 변경되지 않음.

### View에 데이터 전달
``` jsp
// View
<form action="save">
```
- action은 상대 경로
- 현재 URL의 계층 + save가 호출됨

``` java
// Model에 데이터를 보관
request.setAttribute("member", member);
```
- `setAttribute()`를 사용하여 request 객체에 데이터 보관
- `${}`
    - `request.getAttribute("member")`와 같은 역할

### View에 Model List를 전달
- `<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>`를 JSP 파일에 추가
``` jsp
<c:forEach var="item" items="${members}">
    <tr>
        <td>${item.id}</td>
        <td>${item.username}</td>
        <td>${item.age}</td>
    </tr>
</c:forEach>
```

<br /><br />

# 프론트 컨트롤러
- 공통 기능 처리
- 프론트 컨트롤러 서블릿 하나로 요청을 받음
- 요청에 해당하는 컨트롤러를 호출
- 나머지 컨트롤러는 서블릿을 사용하지 않음
- 스프링 웹 MVC의 `DispatcherServlet`이 FrontController 패턴


# 스프링 MVC 구조

## 전체 구조
### `DispatcherServlet`
- 스프링 MVC의 프론트 컨트롤러
- 스프링 MVC의 핵심
- `HttpServlet`을 상속 받아 서블릿으로 동작
    - DispatcherServlet -> FrameworkServlet -> HttpServletBean -> HttpServlet
- 스프링부트는 `DispatcherServlet`을 서블릿으로 자동 등록하면서 모든 경로 `urlPatterns="/"`에 대해 매핑
    - 경로가 자세할 수록 우선 순위가 높음

### 요청 흐름
1. 서블릿 호출시 `HttpServlet`의 `service()` 호출
2. `FrameworkServlet.service()`부터 메서드 호출 시작
3. `DispatcherServlet.doDispatch()` 호출

### SpringMVC 동작 순서
1. 핸들러 조회
    - 요청 URL에 매핑되는 핸들러 조회
2. 핸들러 어댑터 조회
    - 핸들러를 실행할 수 있는 핸들러 어댑터 조회
3. 핸들러 어댑터 실행
4. 핸들러 실행
    - 핸들러 어댑터가 실제 핸들러 실행
5. `ModelAndView` 반환
6. `viewResolver` 호출
7. `View` 반환
    - 뷰의 이름을 물리 이름으로 변환
    - 렌더링을 하는 뷰 객체 반환
8. 뷰 렌더링

### 주요 인터페이스
- 핸들러 매핑: `org.springframework.web.servlet.HandlerMapping`
- 핸들러 어댑터: `: org.springframework.web.servlet.HandlerAdapter`
- 뷰 리졸버: `org.springframework.web.servlet.ViewResolver`
- 뷰: `org.springframework.web.servlet.View`

## 핸들러 매핑 & 핸들러 어댑터
1. 핸들러 매핑으로 핸들러 조회
2. 핸들러 어댑터 조회
3. 핸들러 어댑터 실행

## 뷰 리졸버
``` properties
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```
- `application.properties`의 위의 코드 입력
- 스프링 부트는 `InternalResourceViewResolver`라는 뷰 리졸버를 자동으로 등록
    - 등록할 때 `application.properties`의 `spring.mvc.view.prefix`, `spring.mvc.view.suffix ` 설정 정보 사용


## 시작

## 컨트롤러 통합

## 실용~

## 정리