# **뭐가 공용이고 뭐가 개인용일까**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

현재 IPv4로 모든 주소 할당을 하게된지 상당히 오랜 시간이 지났다.

당연히, 해당 ip 주소가 더이상 남아날 리가 없는 상황이 되었고, 이 부족한 ip 주소 문제를 해결하기 위해 v6도 나오긴 했지만 그 이전에 먼저 생겨난 것이 바로 public ip, private ip의 개념이다.

## public ip

public ip는 간단하게 말해서 인터넷 서비스 공급자(KT등) 이 가정집에 건네주는 라우터 기계, 즉 그 기계가 부여받는 전 세계에 유일무이한 ip 주소를가지고 있는 , 이 아이피가 바로 public ip다. 왜 public이라는 이름이 붙냐면, 이 IP 주소를 통해 전 세계 어느곳이든 외부에서 해당 주소를 타겟으로 신호를 주고받고 할 수 있기 때문이다.

따라서 이 ip 주소를 사용할 때에, 어느 누구든지 들어와서 하이 헬로 하면 좋지 않은 일이 벌어지지 않겠는가

그래서 이를 막기 위해서 방화벽과 같은 접근을 통제하는 보안 시스템을 설치하는 것이다.

## private ip

private ip는 간단히 말해서 public ip로부터 할당받는, 변환된 가상 ip라고 할 수 있다.

요즘에는 집에 인터넷을 쓸 수 있는 기계를 갖고있는 것이 사람 한둘이 아니다.

만약 각 핸드폰별로 고유 아이피가 설정되어야 한다고 가정하자. ip 주소가 남아날 리가 없을것이다.

이를 해결하기 위해, public ip를 기반으로 하여 각 기계에 할당되는 임시 ip 주소가 바로 private ip이며, 주소대역(영역범위) 가 따로따로 설정되어 있다.(어디서 어디까지의 ip를 쓰겠소..)

> public과 private 차이 정리

<table>
    <thead>
        <tr>
            <th></th>
            <th>public</th>
            <th>private</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>할당 주체(누가 이것을 주니)</td>
            <td>인터넷 서비스 공급자</td>
            <td>라우터(공유기)</td>
        </tr>
        <tr>
            <td>할당 대상(누구에게 할당하니)</td>
            <td>공유기</td>
            <td>각각의 네트워크를 사용하는 기기들</td>
        </tr>
         <tr>
            <td>부여받은 Ip의 고유성</td>
            <td>전 세계에서 유일(전 인터넷 상 유일성)</td>
            <td>해당 라우터를 사용하는 네트워크 기기들 상에서 유일</td>
        </tr>
         <tr>
            <td>공개여부</td>
            <td>외부에서 이 아이피로 접근이 가능함</td>
            <td>외부에서 접근할 수 없음</td>
        </tr>
    </tbody>
</table>

이 차이를 보면, private ip로 도대체 뭘한다는지 싶지만 신기하게도

private ip에서 만들어지는 패킷이 네트워크로 전송될때 라우터를 경유해서
해당 라우터의 public ip로 변환되어 외부로 방출되는 것이다.

반대로, 외부에서 들어오는 패킷은 public ip를 경유하여 해당 타겟 private ip로 변환되 전송된다.
