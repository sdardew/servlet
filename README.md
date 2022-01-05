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