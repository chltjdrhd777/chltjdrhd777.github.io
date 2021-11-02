# **아주 중요한 두 패키지**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

개발을 하다보면, 필연적으로 버전이 달라진다거나 해서 코드가 어디에선 읽히는데 어디에선 에러뿜뿜인 상황을 마주하게 된다.

당연히, 이런것을 일일이 하나하나 버전에 맞춰서 변경하는 짓은 미련한것이고 개발자들이 그런 상황을 계속 방치할 이유도 없다.

그래서 생겨난 것이 바로 babel과 webpack이다.

---

## babel

babel은 위에서 언급한, 우리의 최신 디센트 어썸한 코드를 하위버전으로 알아서 변환해주는 아주 고마운 친구다

이 babel의 패키지 구조는 크게 이러하다

<table>
    <tr>
        <th>이름</th>
        <th>기능</th>
    </tr>
    <tr>
        <td>@babel/core</td>
        <td>말그대로 코어다. babel의 기본 스트럭쳐를 가지고 있다</td>
    </tr>
     <tr>
        <td>@babel/cli</td>
        <td>이 바벨 코어를 cli 명령어로 조작하기 위한 패키지다</td>
    </tr>
    <tr>
        <td>@babel/preset-env</td>
        <td>자동으로 환경을 인식해서 어떤 플러그인이 필요한지 말해준다(세부적으로 컨트롤하려면 .browserlistrc 파일에서 정의)</td>
    </tr>
</table>

---

## wepack

개발을 하다 보면, 필연적으로 Import와 export가 남발되게 되는데, 그렇다는 의미는 파일이 많아진다는 뜻이고, 파일이 많아지면 불필요하게 사용되야 하는 메모리의 양도 늘어난다는 소리다.

이 불필요한 공간을 하나로 압축한다는 개념이 바로 웹팩이다.

중요한것은, 서로의 의존성을 파악해서 하나의 파일에 압축한다는 개념이 중요하다. (그 전까지 나는 그냥 파일을 하나로 압축한다고만 생각하고 있었다)

웹팩의 구조는 또한 이러하다

<table>
    <tr>
        <th>이름</th>
        <th>기능</th>
    </tr>
    <tr>
        <td>@webpack/core</td>
        <td>말그대로 코어다. babel의 기본 스트럭쳐를 가지고 있다</td>
    </tr>
     <tr>
        <td>@webpack/cli</td>
        <td>이 webpack 코어를 cli 명령어로 조작하기 위한 패키지다</td>
    </tr>
    <tr>
        <td>babel-loader</td>
        <td>웹팩은 옵션으로 여러가지 파일들을 어떻게 압축할것인지에 대한 config들을 설정할 수 있는데, 이 중에서 특히나 파일을 압축할 때에 자동으로 babel을 통해 파일들의 코드를 다운그레이드하면서 압축할 수 있도록 하는 패키지이다</td>
    </tr>
</table>
