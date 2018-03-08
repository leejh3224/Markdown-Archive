# sendGrid usage

## 들어가며

sendGrid 는 메일을 보내기 위해 필요한 라이브러리다.

도큐먼트도 훌륭하고, 무료 quote 도 꽤 많다.

사용하기 간단하고 기능도 찾으면 금방금방 나오니 모든 조건을 다 갖췄다고 할 수 있다.

## 예시

wix 에서 form submission 후에 메일을 보내는 예제를 따라 만들다보니 생각보다 꽤 막히는 부분이 몇 가지 있었다.

특히 예시에서는 multiple recipients 에게 mulitple mail 을 보내는 부분이 설명이 안 돼있었다.

그래서 한 번 만들어봤다...

핵심은 sendGrid 의 personalization 부분이다.

sendGrid 는 여러 통의 메일을 보내는 기능을 personalization 을 통해 지원한다. ([sendGrid 의 personalization](https://sendgrid.com/docs/Classroom/Send/v3_Mail_Send/personalizations.html))

wix version:

```js
  // sendGrid.js
  // 직접적으로 sendGrid api 를 이용해서 메일을 전송하는 파트

  import { fetch } from 'wix-fetch';

  export function sendWithService(key, sender, email1 ,email2) {
    const url = "https://api.sendgrid.com/v3/mail/send"; // sendGrid v3 api

    const headers = {
      Authorization: `Bearer ${key}`, // api 이용을 위해서는 Authorization header가 필요하다.
      "Content-type": 'application/json',
    };

    const data = {
      personalization: [email1, email2],
      from: {
        email: sender,
      },
      content: [
        {
          type: "text/plain" // text/html 형식도 지원
          value: "{{content}}"
          /*
           * sendGrid는 여러 개의 컨텐트 전송을 지원하지 않는다.
           * 이를 workaround하기 위해서 sendGrid의 substitution tag를 이용한다.
           * 이런 식으로 하면 서로 다른 사람에게 서로 다른 content를 전송할 수 있다.
           */
        }
      ],
    };

    const request = {
      method: "post",
      headers,
      body: JSON.stringify(data) // json type 으로 변환
    }

    return fetch(url, request)
    .then(response => response.json());
  }

  // email.jsw
  // sendWithService를 이용하기 위한 wrapper

  import { sendWithService } from 'backend/sendGrid';

  export function sendBulkEmail(email1, email2) {
    const key = 'YOUR_API_KEY';
    const sender = 'SENDER_EMAIL';

    return sendWithService(key, sender, email1, email2);
  }

  // form page code
  // wix 에서 지원하는 page code

  import { sendBulkEmail } from 'backend/email';

  $w.onReady(function () {
    $w('#applicationsDataset').onAfterSave(sendFormData);
  }); // js의 event trigger 같은 개념

  function sendFormData() {

    // email1
    const subject = 'SUBJECT1';
    const body = 'BODY1';
    const recipient1 = 'RECIPIENT1_EMAIL';

    // email2
    const subject2 = 'SUBJECT2';
    const body2 = 'BODY2';
    const recipient2 = 'RECIPIENT2_EMAIL';

    const email1 = {
      to: [{
        email: recipient1,
      }],
      subject: subject1,
      substitutions: {
        "{{content}}": body, // substitution tag를 사용하면 mustache(template) syntax를 사용할 수 있다.
        // 즉 앞서 메일 전송 시 content에 {{content}}로 표시되어있던 부분이 body로 치환되는 것이다.
      }
    };
    const email2 = { // email1과 동일한 방식으로 해줌.
      to: [{
        email: recipient2,
      }],
      subject: subject2,
      substitutions: {
        "{{content}}": body2,
      }
    };

    return sendBulkEmail(email1, email2);
  }
```

node js 버전의 경우 wix version 과 크게 다르지 않다.
form submission 후에 데이터를 넘겨주는 부분이나 wix-fetch 를 사용한 부분을 js fetch 로 바꿔주면 된다.

### 추가

본격적으로 사용하기에 앞서 api test 를 해보고 싶으신 분들은 postman 을 한 번 사용해보자.
api 전송 테스트 프로그램인데 굉장히 유용하다!
