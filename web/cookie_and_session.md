# 쿠키와 세션, 토큰

## Session / Cookie 방식

Session과 Cookie는 별도의 개념이 아니며, Session은 Cookie를 사용할 수 있다.

- 세션과 쿠키가 나온 배경은, stateless한 http의 connection을 유지하기 위함임.
    - HTTP/1.1의 Keep-Alive를 사용하면 상태를 유지할 수 있음. (handshake 과정 생략)

### Cookie

---

**일종의 서버와 브라우저가 대화하기 위한 수단**

- 지워져도 되고, 가로채더라도 상관 없는 정도의 정보들을 브라우저에 저장
- 브라우저가 서버에 요청할 때마다 필요한 정보조각을 헤더에 포함하여 보내는 것

**세션 쿠키** : 브라우저 닫으면 자동으로 삭제됨

**영속 쿠키** : 지정한 만료일이 되면 삭제.

브라우저 닫고 다시 열었을 때 로그인 상태로 남겨두고 싶으면 영속 쿠키에 세션 ID 저장

### Session

---

- 서버 안에서 다뤄지는 정보
- 사용자나 다른 누군가에게 노출되면 안되는 정보들은 세션으로 서비스 제공자가 직접 관리

### Cookie와  Session

---

Cookie로 인증한다: 요청할 때 사용자 정보를 포함해, 응답에 필요한 모든 정보 같이 줌. (사용하면 안됨)

Session로 인증한다: 내가 누군지만 알려주고, 서버에 저장된 요청자 정보 활용 (Session Storage 필요)

**Session Tracking Mode**

서버가 클라이언트 ID를 어떤 방법으로 추적할 것인가?

- 쿠키 사용 모드(디폴트, 권장)
- URL Rewriting 모드
- SSL 모드

<img width="690" alt="image" src="https://user-images.githubusercontent.com/52846807/190072825-5b8fd49e-121b-4054-ba82-e47c92cfc7a7.png">

### 장단점

**장점**

- 쿠키가 담긴 HTTP 요청이 도중에 노출되더라도 쿠키 자체는 유의미하지 않음. 중요 정보는 서버 세션 스토리지에 존재.

**단점**

- 세션 하이재킹 공격 가능
    - HTTPS 사용해야 함
    - 세션에 유효시간 부여
- 추가적인 저장공간을 요구하고 stateful하게 되어서 scale-out 하기 어려워짐.
    - 세션을 가지고 scale-out 할때는 세션 클러스터 사용
    - 세션 스토리지를 다중 서버로부터 분리
    
    [다중 서버 환경에서 Session은 어떻게 공유하고 관리할까? - 2편(Sticky Session, Session Clustering, Session Storage 분리)](https://hyuntaeknote.tistory.com/6)
    

## JWT 토큰

---

**인증을 위해 사용되는 암호화된 문자열**

- 사용자가 인증에 성공하면 서버는 토큰을 생성해서 클라이언트로 보냄
- 토큰 요청을 받은 서버는 토큰이 유효한지 확인
    - Secret Key를 사용하여 토큰이 위조되었는지 검사 (Payload 그대로인지 확인 가능)
    - 유효 기간 지나지 않았는지 검사

<img width="690" alt="image" src="https://user-images.githubusercontent.com/52846807/190073117-3c249338-a194-4cf6-ba40-93de6b71dadc.png">
<img width="703" alt="image" src="https://user-images.githubusercontent.com/52846807/190073183-596929f2-cc11-4f28-a921-cbd6b0c8c4bc.png">

### 장단점

**장점**

- 별도의 저장소 관리가 필요하지 않음
- oauth 등 토큰 기반의 다른 인증 시스템에 접근 가능. (확장성)
- Stateless
    - 하지만 refresh token 사용이나 blacklist 관리 시, token 저장하게 됨.

**단점**

- 이미 발급된 JWT를 만료시킬 수는 없음. 세션/쿠키의 경우 만일 쿠키가 악의적으로 이용 시, 해당하는 세션을 지워버리면 됨.
- Payload는 따로 암호화되지 않기 때문에 (그저 base64 인코딩, 디코딩) 누구나 정보 확인 가능

### 기타 키워드

---

- 세션 하이재킹(보안 이슈)
- 세션 클러스터링
    - Sticky Session
    - All-To-All
