# 써드파티 API 사용하기

## IP whitelist 가 있을 경우 해결책

## 1. 최고의 문자 API 서비스를 찾아서

웹 개발에서 빼놓을 수 없는 요소가 뭐냐고 물어본다면 나는 무조건 API 라고 할 것이다.

웬만한 개발 튜토리얼은 대부분 API 를 호출하고, 그 결과를 보여주는 방법을 가르치는데서 부터 시작하기 때문이다.

그 과정에서 cors 나 http request methods 혹은 json 같은 개념들과 친숙해지고 거대한 애플리케이션을 만드는 기초를 알게 된다.

나는 React.js 와 Node.js 로 몆 차례 사이드 프로젝트를 진행했었다.

몇 개는 보여주기 민망한 수준이고 그나마도 시간이 지나면서 webpack 설정이 바뀌거나 boilerplate 가 deprecated 되면서 쓸 수 없게 됐다.

최근에는 Wix 를 기반으로 가사도우미 예약 서비스를 만들면서 문자 전송 API 를 사용할 일이 생겼다.

고객이 에약 페이지에서 예약날짜, 서비스 유형이나 전화번호 등을 입력하고 나면 그 번호로 예약 내용에 대한 문자를 전송하는 간단한 서비스였다.

처음에는 wix 내의 문자 전송 서비스를 사용하려 했다.

근데 wix 앱 마켓에 문자 전송 관련 앱이 단 하나밖에 없었을 뿐더러 그 앱조차도 평가가 굉장히 안 좋았다;;

SMS Hero 라는 앱이었는데 사용자 후기를 읽어보니 성난 개발자들의 1 점 러시밖에 보이지 않았고 총 평점도 2.5 점에 불과했다.

게다가 SMS Hero 팀의 답변이 달린 게시물도 몇 개 없는 걸로 보아 믿을 수 없다고 판단하고 빠르게 다른 서비스들을 알아봤다.

나는 오로지 문자 전송만을 위한 서버를 만드는건 시간낭비 돈낭비라는 생각을 했고 빠르게 REST API 로 문자 서비스를 제공하는 업체들을 살펴봤다.

처음에는 해외 업체를 위주로 살펴보았다.

그 중 유명한 몇 군데를 들어보자면

1. Twilio

* 말 할 필요가 없는 SMS 서비스의 끝판왕. SMS 서비스뿐만 아니라 "Programmable Voice"라는 전화 서비스도 제공한다. 게다가 고객 상담도 훌륭하기 이를데 없다.

* 문제는 내부 로직의 문제인지는 몰라도 naver 메일로 가입이 불가능하다는 것. 이 문제때문에 계속 가입이 불가능하다는 오류메시지만 보다가 고객센터에 메일로 날짜를 예약했고 약속했던 시간이 돼도 메일이 오지 않아 포기했었다 ...

* 근데 그 다음날 아침에 갑자기 국제 전화가 오는 것이었다. 알고보니 내가 예약한 시간이 KST(한국표준시)가 아닌 PST(태평양표준시)였던 것. Twilio 기술자분이 꽤나 친절한 목소리로 무슨 문제가 있냐고 물어보기에 이런 저런 사정을 설명했다. 결론적으로는 naver 메일로는 가입이 불가능하니 gmail 로 해보라는 것. 그래서 gmail 로 가입을 하니 잘 됐다. 그 중에 정말 Twilio 의 서비스에 감탄했는데 내가 보내는 메일마다 5~10 분내로 지속적으로 피드백을 받을 수 있었고 꽤 빠르게 해결책을 제시해줬다.

* 자 이쯤에서 내가 왜 Twilio 를 포기했는지 얘기해야될 것 같다. 고객선테 서비스도 좋고, 유명하기까지 하지만 Region 이 지원되지 않는다. 즉 한국번호를 넣으면 서비스를 이용할 수가 없다. 그렇게 이틀을 날리고 상큼하게 패스.

2. Aws SNS

* Twilio 의 대안으로 살펴본 문자 서비스. 일단 굉장히 사용이 간편했고 또 한국번호로도 사용이 가능했다.

* 다만 가격대가 상당히 비쌌는데 SMS 의 경우 한 통에 38 원쯤했다.

* 다른 한국업체들의 서비스 이용료가 8 원 ~ 20 원 중반쯤인걸 감안하면 꽤나 비싼편.

* 물론 아마존은 대기업이고 훌륭한 수준의 서포팅과 질 좋은 도큐멘테이션의 덕을 볼 수 있는 건 큰 장점이다.

* 만약 개인 프로젝트였다면 망설이지 않고 썼을 듯.

3. Coolsms

* 세상에서 가장 안정적이고 빠른 문자서비스를 제공한단다.

* SMS 서비스를 검색했을 때 가장 상단에 뜨길래 일단 한 번 가입해봤다.

* 들어가보니 실패건에는 과금하지 않는다고 하고, 문서가 정~말 깔끔하게 정리돼있었다. (10 점 만점에 9 점)

* 다만 실제로 사용해보니 서버 환경이 아닌 클라이언트 환경에서는 사용이 어려운 점이 많았다.

* 기본적으로 REST API 요청을 보내려면 인증을 해야하는데 인증 필드에 api key 뿐만 아니라 timestamp 와 임의의 salt 를 기반으로 한 md5 hmac 을 실어서 요청해야했는데 이는 서버 환경이 아니고서야 꽤나 부담이 되기 때문.

* 또 결정적으로 최신버전이라고 광고하는 3.0 버전이 사용이 어렵다는 사실을 문의메일을 보낸 하루 뒤에야 알게 됐기 때문이다. (게다가 홈페이지에 고객센터 전화번호가 없다 ... 문화충격!)

* API 3.0 버전을 사용하는 중에 계속해서 4xx 에러가 뜨길래 api key 를 잘못 넣거나 hmac 을 잘못 넣은줄 알고 계속 삽질했는데 답변 메일을 읽어보니 "아쉽게도 저희 서비스 내부 사정으로 인하여 현재는 3.0 버전 사용이 힘든 상태입니다."란다. 아니 그럴거면 점검 중입니다. 사용이 불가능하니 2.0 버전을 사용해주십시오. 라는 한 마디만 있어도 얼마나 좋은가.

* 아무튼 Coolsms 는 그렇게 포기하게 됐다.

4. Aligo

* 내 요구사항

  * REST API 형태로 서비스가 제공되는가
  * Client javascript 만으로 이용가능한가
  * 가격이 저렴한가
  * 테스트 건에 대해서는 과금하지 않는가 또 테스트 전송이 제한되어있지 않은가

* 위의 네 가지 요구사항을 만족시키는 서비스는 알리고밖에 없었다.

* 단문 8.4 원 / 장문 25 원의 말도 안되는 저렴한 가격에 한 번만 읽어봐도 바로 사용가능한 api spec 까지 정말 모든게 만족스러웠다.

## 2. 문제의 시작

이렇게 Aligo 를 발견하고 모든게 순탄할 줄 알았던 개발은 또다른 난항을 겪게 된다.

바로 내가 API spec 에 나오는 curl 구문을 모른다는 것 ...

그렇게 curl 구문을 공부하고 또 한 차례 해맨 결과 간신히 POSTMAN 으로 올바른 결과를 받아볼 수 있었다.

그런데 포스트맨으로 요청을 보낼 경우 응답의 message 부분이 decode 되지 않은 상태로 돌아오는 걸 발겼했다.

이를테면 (https://apis.aligo.in/remain/으로 요청한 결과입니다.)

```
  {"result_code":-101,"message":"\uc778\uc99d\uc624\ub958\uc785\ub2c8\ub2e4.-IP","SMS_CNT":0,"LMS_CNT":0,"MMS_CNT":0}
```

이럴 경우 js 의 decodeURI 함수를 이용해 message 부분을 해독하면 "인증오류입니다.-IP"라는 상큼한 결과를 받아볼 수 있다.

이 에러문구가 의미하는 바는 꽤 명확한데 이 말인 즉슨 내 컴퓨터의 IP 주소가 문제가 있다는 것이다.

이는 대부분의 문자 전송 서비스가 등록되지 않은 IP 주소를 차단하기 때문인데

알리고 상단의 [연동형 API]-[신청/인증] 메뉴에서 발송 IP 에 현재 IP 주소를 추가해주면 된다.

IP 주소를 입력해주면

```
  {"result_code":1,"message":"success","SMS_CNT":5936,"LMS_CNT":1925,"MMS_CNT":831}
```

음 5 만이면이면 SMS 를 6000 통, LMS 를 2000 통 정도 보낼 수 있네요. 혜자 서비스 인정합니다.

추가로 API spec 을 제대로 읽지 않는 나같은 개발자를 위한 꿀팁을 얘기하자면 테스트 문자를 전송할 때 data 로 testmode_yn=Y 를 주면 공짜로 테스트해볼 수 있다는 점!

## 3. Wix 연동

이제 api 서비스가 제대로 잘 작동되는 걸 확인했으니 다음 단계로 wix 에다 문자서비스를 심어주자.

wix 는 기본적으로 커스터마이징 지원이 굉장히 빈약한데 워드프레스처럼 별의별 플러그인이 다 존재하는 서비스에 비교하면 정말 세발의 피다.

그래도 나름 빛이 있다면 wix-code 라는 서비스를 지원한다는건데 이를 통해 html code 추가나 써드파티 api 호출하는 백엔드 코드 작성이 가능하다는 점 되겠다.

참조: wix 도움말 센터에 가면 [HTML 코드 추가하기](https://support.wix.com/ko/article/html-%EC%BD%94%EB%93%9C-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0) 에 대한 추가정보를 살펴볼 수 있다.

아무튼 wix 는 js fetch 를 대신하는 wix-fetch 를 제공한다.

그리고 wix-fetch 를 사용하면 backend 에 웹모듈(.jsw) 파일을 만들어 써드파티 api 요청을 손쉽게 할 수 있다.

이를테면 wix 공식문서의 email 서비스는 다음과 같이 구현이 가능하다.

```js
//sendGrid.js

import { fetch } from 'wix-fetch'

export function sendWithService(key, sender, emails) {
  const url = 'https://api.sendgrid.com/v3/mail/send'

  const headers = {
    Authorization: `Bearer ${key}`,
    'Content-type': 'application/json',
  }

  const data = {
    personalizations: emails,
    from: {
      email: sender,
    },
    content: [
      {
        type: 'text/plain',
        value: '{{content}}',
      },
    ],
  }

  const request = {
    method: 'post',
    headers: headers,
    body: JSON.stringify(data),
  }

  return fetch(url, request).then(response => response.json())
}
```

이러면 평소에 node js 에서 api 요청을 하듯이 간단하게 코드를 쓸 수 있다.

위의 예시 코드는 아마도 공식문서의 코드와 좀 다를 수 있는데 내 코드는 sendGrid 버전 3 api 를 사용하기 때문이다.

시간이 난다면 sendGrid v3 api 사용법에 대해서도 간략히 쓸 예정.

Wix-fetch 얘기로 다시 넘어와서 wix-fetch 로 Aligo 에 문자를 전송하는 코드를 작성했다.

근데 IP 주소를 추가해줘도 인증오류만 날 뿐 제대로 작동하지 않는 것이었다.

그래서 대안으로 html code 조각을 통해 js fetch 를 사용해보기로 했다.

```html
  <html>

  <script>
    window.onmessage = (event) => {
      checkReamin()
    }

    function checkRemain() {
      const key = 'API_KEY'
      const userid = 'USER_ID'

      return fetch('https://apis.aligo.in/remain/', {
        method: 'POST',
        headers: new Headers({
          'Content-Type': 'application/x-www-form-urlencoded'
        }),
        mode: 'no-cors',
        body: `key=${key}&userid=${userid}`
    }
  </script>

</html>
```

mode 를 no-cors 로 해주니 wix-fetch 와는 다르게 잘 작동했다.

아직도 wix-fetch 가 왜 작동하지 않는지는 모르겠지만 예상으로는 json 타입의 body 가 아니면 제대로 작동하지 않는 것 같다.

Aligo 서비스는 body 가 x-www-form-urlencoded 된 형태여야만 응답했기 때문이다.

즉 일반적으로 json 형태의 요청을 보탤 때는 JSON.stringify(request object) 의 형태로 보내는데 반해

x-www-form-urlencoded 된 형태의 body 는 key=123&userid=123lewe 이런 식으로 key, value 를 =과 &으로 구분한 형태로 보낸다.

### 4. 또 다른 문제

이제 모두 해결이 되었다고 생각하고 기쁨에 잠겨있을 찰나 갑자기 한 가지 생각이 머리를 스쳤다.

"허용된 IP 주소에서만 문자를 보낼 수 있으면 클라이언트의 IP 주소에 따라 문자 발송 IP 주소가 달라지는 지금 내 상태로는 문자가 제대로 전송이 안 돼지 않을까?"

아니나다를까 식당 와이파이로 예약을 시도해보니 문자가 전송돼지 않았다.

그래서 어떻게하면 고정된 IP 주소로 요청을 보낼 수 있을까 고민하던 중에 Amazon VPC 서비스를 알게 됐다.

여기서 VPC 란 Virtual Private Cloud 의 약자로 가상 네트워크인데, Private 한 즉 계정 전용 네트워크이다.

그리고 이 VPC 가 있으면 EC2 인스턴스나 RDS 같은 aws 의 리소스를 VPC 에서 실행할 수 있다.

내 경우에는 ApiGateway 에서 VPC 를 통해 Lambda 를 실행시키기 위한 목적으로 VPC 를 사용하게 됐다.

구조는 대략

```
  사용자의 디바이스 ---------> ApiGateway ------> Lambda private subnet --------> NAT Gateway ------> 알리고 서비스
```

위와 같다.

여기서 주목할 부분은 Lambda 의 private subnet 에서 NAT Gateway 를 통해 인터넷 서비스(알리고)와 연결되는 부분인데

NAT Gateway 는 elastic IP 와 연계해서 outbound 인터넷 요청에 대해 고정된 IP 주소를 쓸 수 있다는 것이다!

이렇게 해두면 기본적으로 유동적인 IP 주소를 이용하는 기본 Lambda 서비스와 달리 IP 주소를 고정해둘 수 있다.

참고자료: [고정된 IP 주소로 람다 사용하기](AWS Lambdas with a static outgoing IP)

영어로 된 글이긴 하지만 내가 가장 많은 도움을 받은 글이기도 하고 또 설명도 굉장히 상세해서 공유해본다.

이 튜토리얼을 따라가면 VPC 세팅을 모두 마칠 수 있다.

람다나 ApiGateway 그리고 VPC 설정은 꽤 까다롭다. 하지만 한 번 설정을 마쳐두면 serverless computing 의 장점을 만끽할 수 있다.

만약 가용 ip 주소가 제한된 상태에서 api 서비스를 사용해야할 일이 있다면 VPC 를 사용해보자.

### 삽질을 줄여주는 Q&A

Q. 람다에서 VPC 세팅을 하려하니 "Your role does not have VPC permissions. Please go back and select “Basic with VPC” under the role dropdown to add these permissions." 에러가 발생합니다.

A. 이 경우 IAM 콘솔에서 람다를 실행하는 역할에 "AWSLambdaVPCAccessExecutionRole" 정책을 추가해주면 됩니다!
만약 정책을 추가해도 설정이 저장되지 않는다면 역할을 삭제하고 다시 설정해보시거나 좀 시간이 지난 뒤에 다시 해보시기 바랍니다.

Q. API 요청을 하니 Timeout 에러가 발생하네요 ㅜㅜ

A. 람다는 기본적으로 30 초의 타임아웃이 설정돼있습니다. 다만 10 초만에 Timeout 에러가 발생하는 경우가 있는데 이는 아예 요청을 보낼 수 없다는 뜻입니다. 이 경우 VPC 서브넷의 라우트 테이블 설정을 잘 살펴보세요.

혹시 NAT 이나 Internet Gateway 가 Target 으로 설정이 안 돼있다면 추가해줍니다.

Private 서브넷은 NAT/Public 서브넷은 IGW(인터넷게이트웨이)가 모든 요청(0.0.0.0/0)에 대해 각각 라우트 테이블에 등록되어있어야합니다.

Q. 람다에 Axios 나 Request 같은 api client 라이브러리를 사용할 순 없나요?

A. 람다는 inline 코드 편집뿐만 아니라 zip 파일 업로드도 지원합니다.

node_modules 폴더와 index.js 파일을 작성하시고 zip 파일로 압축한 뒤에 Function Code 부분의 Code entry 에서 zip 파일 업로드 선택을 통해 업로드해주세요.

Q. ApiGateway 에 auth 를 추가할 수 있나요?

A. 네. 아무나 우리 람다를 써대면 화나죠. ApiGateway 의 각 리소스의 해당 메쏘드(GET, POST 등)에 들어가면 네모 4 개가 보입니다.

1 번에 들어가서 Authorization 에 AWS_IAM(아마존 access_key 와 access_secret_key 를 요구하는), API 키 필요여부에 true 를 설정해줍니다.

이제 API 요청시 header 에 x-api-key=xxx 이런식으로 요청을 보낼 수 있습니다.

API_KEY 는 좌측의 API_KEY 메뉴에서 action 을 통해 하나 생성해줍시다.

그 다음에 아까 Authorization 을 설정했던 1 번 상자로 들어가서 아래에 Http request header 를 추가하는 부분에 x-api-key 를 넣어줍니다.

이제 client 의 test 를 누를 때마다 x-api-key 헤더의 값을 입력하는 input 이 보일 것입니다.

Q. 콘솔에서 시도하니 "{"message": "Could not parse request body into json: Unrecognized token \'sdsdsfdsfwgwgw\': was expecting (\'true\', \'false\' or \'null\')\n at [Source: [B@56ad7694; line: 1, column: 42]"}" 이런 에러가 발생합니다.

A. curl 구문으로 json 바디를 보내려면 각각의 " 마다 escape 해줘야합니다. 예를 들면 "{\"key\": \"API_KEY\"}" 이런 식으로 말이죠.

Q. POSTMAN 으로 api 요청을 테스트해보고 싶어요!

A. Authorization 탭으로 가보면 Type 에서 AWS Signature 를 설정해줄 수 있습니다.

Access_key 와 secret_key 를 입력해주고 AWS_REGION 에는 ap-northeast-2 (서울인 경우) 혹은 다른 리전을 입력해주고 Service Name 에는 execute-api 를 입력해줍니다. 이제 헤더 탭을 확인해보면 자동으로 인증정보가 등록되었습니다.

이제 요청을 보낼 차례인데 바디는 raw 를 선택하고 필터에서 JSON 을 선택해준 뒤 입력해주면 됩니다.
