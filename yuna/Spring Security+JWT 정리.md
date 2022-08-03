# Spring Security

> Spring과 별개로 작동하는 사용자 인증 및 접근 제어 관련 보안 담당 프레임워크
> 

### Spring Security Architecture
<img src="https://user-images.githubusercontent.com/26339069/182656749-8785aa0a-0ef1-4887-b4f4-bc2d58544a44.png" width="600">

1. 사용자가 입력한 사용자 정보를 가지고 인증을 요청한다 (Http Request)
2. AuthenticationFilter가 요청을 가로챈다
3. AuthenticationFilter에서 요청의 Username, password를 이용하여 UsernamePasswordAuthenticationToken(인증용 객체)을 생성한다
4. AuthenticationFilter는 요청을 처리하고 AuthenticationManager의 구현체 ProviderManager에 Authentication과 UsernamePasswordAuthenticationToken을 전달한다
5. AuthenticationManager는 토큰을 AuthenticationProvider에게 토큰을 넘겨준다.
6. AuthenticationProvider는 UserDetailsService로 토큰의 사용자 아이디(username)을 전달하여 DB에 존재하는지 확인한다. 이 때, UserDetailsService는 DB의 회원정보를 UserDetails 객체로 반환한다.
7. AuthenticationProvider는 반환받은 UserDetails 객체와 실제 사용자의 입력정보를 비교한다.
8. 인증이 완료되면 권한과 사용자 정보를 담은 Authentication 객체가 반환된다.
9. AuthenticationFilter까지 Authentication 정보를 전달한다.
10. Authentication 객체를 SecurityContextHolder에 담은 이후 AuthenticationSuccessHandler를 실행한다.<br>(실패시 AuthenticationFailureHandler를 실행한다.)

> 결국 SecurityContextHolder 세션 영역에 있는 SecurityContext에 Authentication 객체를 저장한다.<br> 
> 세션에 사용자 정보를 저장한다는 것은 전통적인 ***세션-쿠키 기반의 인증 방식***을 사용한다는 것을 의미한다.

---

### Spring Security Filter Chain

<img src="https://user-images.githubusercontent.com/26339069/182656763-de056735-3be3-45eb-9318-4876326ae286.png" width="600">

- Spring Security는 표준 서블릿 필터를 사용하며, 다른 요청들과 마찬가지로 HttpServletRequest와 HttpServletResponse를 사용한다.
- 서블릿 컨테이너의 필터는 Dispatch Survlet으로 가기 전에 먼저 적용된다.
- 필터는 여러개가 연결되어 있어 Filter chain이라고도 불린다.
- 모든 Request들은 Filter chain을 거쳐야지 Survlet에 도착하게 된다.
<details>
<summary>더보기</summary>
        
    Spring Security는 서비스 설정에 따라 필터를 내부적으로 구성한다. 
        
    각 필터마다 역할이 있고, 필터 사이의 종속성이 있어서 순서가 중요하다.  
        
    XML Tag를 이용한 namespace 구성을 사용하는 경우 필터가 자동으로 구성된다.    
        
    하지만 namespace 구성이 지원하지않는 기능을 써야하거나 커스터마이징된 필터를 사용해야 할 경우 명시적으로 빈을 등록 할 수 있다. 
        
    클라이언트가 요청을 하면 DelegatingFilterProxy가 요청을 가로채고 Spring Security의 빈으로 전달한다.  
        
    이 DeletgatingFilterProxy는 web.xml과 applicationContext 사이의 링크를 제공한다.    
        
    그러니까 DelegatingFilterProxy는 Spring의 애플리케이션 컨텍스트에서 얻은 Filter Bean을 대신 실행한다. 
        
    이 Bean은 javax.servlet.Filter를 구현해야한다.(jwtAuthenticationFilter)
        
</details>

### **Spring Security Filter**

> Spring Security는 필터 기반으로 수행된다
#### 필터 vs 인터셉터
- 둘의 차이는 실행되는 시점의 차이이다 
- 필터: dispatcher servlet으로 요청이 도착하기 전에 동작
- 인터셉터: dispatcher servlet을 지나고 controller에 도착하기 전에 동작

```
 💡 필터를 다 외우기보다 대략적으로 이해하자!
```

| Filter | Description |
| --- | --- |
| HeaderWriterFilter | HTTP 헤더를 검사 |
| SecurityContextPersistenceFilter | SecurityContextRepository에서 SecurityContext를 가져오거나 저장 |
| LogoutFilter | 로그아웃 여부 체크 |
| (UsernamePassword)AuthenticationFilter | (아이디와 비밀번호를 사용하는 form 기반 인증) 로그인 시도 체크<br>- AuthenticationManager를 통한 인증 실행<br>-  성공 시, 얻은 Authentication 객체를 SecurityContext에 저장 후 AuthenticationSuccessHandler 실행<br>- 인증 실패 시, AuthenticationFailureHandler 실행 |
| DefaultLoginPageGeneratingFilter | 인증을 위한 로그인폼 URL을 감시 |
| BearerTokenAuthenticationFilter | Authorization 헤더에 Bearer 토큰 존재시 인증 처리 |
| BasicAuthenticationFilter | Authorization 헤더에 Basic 토큰 존재시 인증 처리 |
| RequestCacheAwareFilter | request 이력 캐시에 저장 |
| SecurityContextHolderAwareRequestFilter | HttpServletRequestWrapper를 상속한 SecurityContextHolderAwareRequestWapper 클래스로 HttpServletRequest 정보를 감싼다. SecurityContextHolderAwareRequestWrapper 클래스는 필터 체인상의 다음 필터들에게 부가정보를 제공한다. |
| AnonymousAuthenticationFilter | 이 필터가 호출되는 시점까지 사용자 정보가 인증되지 않았다면 인증토큰에 사용자가 익명 사용자로 나타난다 |
| SessionManagementFilter | 인증된 사용자와 관련된 모든 세션을 추적 |
| ExceptionTranslationFilter | 보호된 요청을 처리하는 중에 발생할 수 있는 예외를 위임하거나 전달 |
| FilterSecurityInterceptor | 대부분 가장 마지막에 사용되어 들어온 request에 대한 자격 체크와 리턴한 결과를 보내도 될지 점검 |

➕ 새로운 Filter를 생성하려면 securityConfig에 필터 체인을 추가 등록하면 된다!!


# JWT (JSON Web Token)

### Authentication(인증) vs Authorization(인가)

- Authentication(인증): 로그인과 같이 ***아이디, 비밀번호*** 등을 통해 특정 서비스에 일정 권한이 주어진 ***사용자임을 인증 받음***
- Authorization(인가): ***이미 인증받은 사용자***가 서비스의 특정 기능을 사용하기 위한 ***권한을 허가받는 과정***
---
### JWT
- JWT는 인증보다 **인가**에 연관된 기술이다
- JWT는 사용자를 식별하기 위한 **토큰** 기반 인증방법이다
- 토큰 자체에 사용자의 권한 정보나 서비스를 사용하기 위한 정보가 포함된다는 특징이 있다
- JWT를 사용하면 RESful과 같은 무상태(Stateless)인 환경에서 사용자 데이터를 주고 받을 수 있게 된다

- JWT는 세파트로 나누어지며, 각 파트는 점으로 구분하여 표현된다. (예) xxxxx.yyyyy.zzzzz

    > 세파트는 순서대로 ***헤더(Header), 페이로드(Payload), 서명(Singture)*** 으로 구성된다.
    
    - Header: JWT를 어떻게 해석해야 하는지 명시 (토큰의 타입과 해시 암호화 알고리즘으로 구성)
    - Payload: 토큰의 바디에 포함할 정보
    - Singture: Base64 URL 인코드된 헤더와 페이로드 그리고 별도 생성한 JWT secret key를 헤더에 지정된 암호 알고리즘으로 암호화한 것
``` java
JWT = B64(Header).B64(Payload).B64(Sig) // B64(Base64 URL-safe Encode)
```
- #### 🚧 주의할 점
  - Payload는 암호화하지 않는다. 
  - 그래서 서명없이도 누구나 열어볼 수 있으므로 Payload에는 보안이 중요한 데이터는 넣으면 안된다. 
  - **필요한 최소한의 정보만 담아야 한다.**
  
---


### JWT 사용 흐름

1.  클라이언트 사용자가 id와 password를 입력해 로그인 시도

2. 서버는 요청을 확인하고, secret key를 통해 Access token을 발급

3. JWT 토큰을 클라이언트에 전달

4. 클라이언트에서 API 을 요청할때,  클라이언트가 Authorization header에 Access token을 담아서 보냄

5. 서버는 JWT Signature를 체크하고 Payload로부터 사용자 정보를 확인해 데이터를 반환

---
### Access token & Refresh token

> 클라이언트의 로그인 정보를 서버 메모리에 저장하지 않기 때문에 토큰기반 인증 메커니즘을 제공한다.

처음 사용자를 등록할 때 ***Access token***과 ***Refresh token***이 모두 발급되어야 한다.

- Access token: 매번 인가 받을 때 사용하는 토큰. 보통 수명이 짧다
- Refresh token: Access token의 수명이 다했을 때, Access token을 재발행 받기 위한 토큰. 보통 2주 정도 기간이 길게 잡힌다

---

> 참고자료<br>[1] [http://www.opennaru.com/opennaru-blog/jwt-json-web-token/](http://www.opennaru.com/opennaru-blog/jwt-json-web-token/)<br>[2] [https://velog.io/@syoung125/JWT-토큰이란](https://velog.io/@syoung125/JWT-%ED%86%A0%ED%81%B0%EC%9D%B4%EB%9E%80)<br>[3] [https://velog.io/@jkijki12/Spirng-Security-Jwt-로그인-적용하기](https://velog.io/@jkijki12/Spirng-Security-Jwt-%EB%A1%9C%EA%B7%B8%EC%9D%B8-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0)<br>[4] [https://velog.io/@shinmj1207/Spring-Spring-Security-JWT-로그인](https://velog.io/@shinmj1207/Spring-Spring-Security-JWT-%EB%A1%9C%EA%B7%B8%EC%9D%B8)<br>[5] [https://devfunny.tistory.com/615](https://devfunny.tistory.com/615)
