# Spring MVC - 예외 처리
## 서블릿 예외 처리 - 시작
- **서블릿은 다음 2가지 방식으로 예외 처리를 지원한다.**
  - `Exception`
  - `response.sendError(HTTP 상태 코드, 오류 메시지)`

### Exception
**자바 직접 실행**
- 자바의 메인 메서드를 직접 실행하면 `main`이라는 이름의 쓰레드 생성
- 실행 도중에 예외를 잡지 못하고 처음 실행한 `main()` 메서드를 넘어서 예외가 던져지면, 예외 정보를 남기고 해당 쓰레드는 종료

**웹 애플리케이션**
- 사용자 요청별로 별도의 쓰레드 할당되고, 서블릿 컨테이너 안에서 실행
- 예외가 발생하고 어디선가 try-catch로 예외를 잡아서 처리하면 문제x
- 만약 애플리케이션에서 예외를 잡지 못하고, 서블릿 밖으로 까지 예외가 전달되면 어떻게 동작할까?
```
WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
```
- 결국 톰캣같은 WAS 까지 예외가 전달된다. 이 때 어떻게 처리해야 할까?
```java
@GetMapping("/error-ex")
     public void errorEx() {
        throw new RuntimeException("예외 발생!"); 
}
```
```
HTTP Status 500 – Internal Server Error
```
- `Exception`의 경우 서버 내부에서 처리할 수 없는 오류가 발생한 것으로 생각해서 HTTP 상태 코드 500을 반환한다.
- 이번에는 존재하지 않는 URL을 호출해보자.
```
HTTP Status 404 – Not Found
```
- 톰캣이 기본으로 제공하는 404 오류 화면을 볼 수 있다.

### response.sendError(HTTP 상태 코드, 오류 메시지)