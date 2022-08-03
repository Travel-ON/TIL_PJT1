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

> 참고자료<br>[1] [http://www.opennaru.com/opennaru-blog/jwt-json-web-token/](http://www.opennaru.com/opennaru-blog/jwt-json-web-token/)<br>[2] [https://velog.io/@syoung125/JWT-토큰이란](https://velog.io/@syoung125/JWT-%ED%86%A0%ED%81%B0%EC%9D%B4%EB%9E%80)
