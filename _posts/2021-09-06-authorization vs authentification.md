# **둘의 차이가 뭘까**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

1. Authentification

authentification은 서버가 특정한 유저를 향해 "너가 바로 내가 원하던 사람이야!" 라고 검증하는 과정이다. 구현방안은 이러하다

- 유저에게 비밀번호와 유저 아이디를 요청한다. 만약 유저가 올바른 정보를 입력한다면, "ok 제대로 된 정보를 알고있네." 라고 서버가 이해한다
- 오직 한 세션에만 유지가 되는 허가를 발행한다(ex 세션 스토리지)
- 외부 라이브러리를 이용하여(jwt같은) 검증을 한다
- 바이오 인증을 한다

authentification의 과정은 일반적으로 우리가 볼 수 있는 인증의 과정으로, 하나 혹은 그 이상의 보안적인 요청을 할 수가 있다.

---

2. Authorization

authorization 은 유저에게 특정 리소스에 대한 접근을 허용하는 절차를 말한다. 예를들어, 한 유저에게 이러이러한 범위까지 다운로드를 허용하지만, 이 이상으로는 다운로드 권한을 허용하지 않는것과 같은 방식을 들 수 있다.
또한 관리자는 특정 페이지에 접근하여 수정이나 업데이트를 진행할 수 있는 특수 권한을 위한 코드를 부여하고, 일반 유저들은 해당 페이지에 접근조차 할 수 없도록 막아두는 것 또한 그 예로 들 수 있다.

즉, authorization은 authentification을 통해 "아 너는 누구구나" 라고 확인한 이후, 이 사람은 여기까지 할 수 있어! 라고 정의해주는 행위와 같다.
