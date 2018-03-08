# Today I Learned ...

## 2018-01-29

### date ISO(ISO 8601) 형태로 변환

[참조 1](https://stackoverflow.com/questions/10645994/node-js-how-to-format-a-date-string-in-utc)

요약:

```js
// current time to ISO string
new Date().toISOString()
```

### lifeCycle method 가 두 번 불림

[참조 1](https://github.com/reactjs/redux/issues/1646)

[참조 2](https://stackoverflow.com/questions/35136836/react-component-render-is-called-multiple-times-when-pushing-new-url)

요약:

```
  The lifecycle books are managed by React. If you see that componentWillMount runs twice it means the component was unmounted between those calls for some reason. You can set a breakpoint in componentWillIUnmount and check the stack trace to see what is causing the component to unmount.

  shouldComponentUpdate를 이용해서 컨트롤함
```

### 댓글의 페이지네이션 로직

[참조 1](https://stackoverflow.com/questions/4421207/mongodb-how-to-get-the-last-n-records/4425163)

[참조 2](https://docs.mongodb.com/manual/reference/operator/aggregation/limit/)

요약:

```
Ascending order = 1 = 예전 ~ 최순 순
Descending order = -1 = 최신 ~ 예전 순
```

### document body 에 event 붙이기

[참조 1](https://stackoverflow.com/questions/32485520/how-can-i-add-a-click-handler-to-body-from-within-a-react-component)

```js
var Component = React.createClass({
  componentDidMount: function() {
    document.body.addEventListener('click', this.myHandler)
  },
  componentWillUnmount: function() {
    document.body.removeEventListener('click', this.myHandler)
  },
  myHandler: function() {
    alert('click')
  },
  render: function() {
    return <div>Hello {this.props.name}</div>
  },
})
```

### mongoose update query 시 updated document 가져오기

[참조 1](https://davidburgos.blog/return-updated-document-mongoose/)

```js
var query = { id: 8 }
var update = { title: 'new title' }
var options = { new: true }
MyModel.findOneAndUpdate(query, update, options, function(err, doc) {
  // Done!
  // doc.title = "new title"
})
```

## 2018.1.30

### cURL

[참조 1](https://bakyeono.net/post/2016-05-02-rest-api-client-for-cli.html)

curl post examples
[참조 2](https://gist.github.com/subfuzion/08c5d85437d5d4f00e58)

js FormData 객체

[참조 3](https://developer.mozilla.org/ko/docs/Web/API/FormData)

```
  cURL은 명령줄 인터페이스 상에서 http 요청을 보내기 위한 언어.
  몇 가지 옵션을 정리하면
  -X method
  -H header
  -d data
  --data-urlencode 엔코딩된 데이터
```

### markdown to pdf

[참조 1](https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf)

요약: 마크다운 문서를 pdf 로 바꿔주는 vscode extension, 화질구지가 되는게 흠이긴 하지만 사용하기 간편.

## 2018.01.31

### js fetch

[알리고](https://smartsms.aligo.in/main.html)라는 문자 서비스를 wix 에서 사용하기 위해 계속 삽질을 해봤지만 끝내 실패했다.

그래서 wix 의 wix-fetch 대신 js 의 fetch 를 사용해봤다. 결과적으론 성공.

그리고 native fetch 의 중요한 속성 중 하나를 파악했다.

mode 에서 'no-cors' 모드를 설정할 수 있다는 것.

단순히 Access-Control-Allow-Origin: \* 헤더를 request 헤더에 설정해도 안 되던 게 갑자기 잘 작동했다. 정확한 원인은 파악 x

## 2018.02.06

### 접속 IP 주소 제한이 있는 Third party API 사용하기

* 포스타입 블로그에 글 작성!

## 2018.02.07

### image file 크기를 효과적으로 줄이기

* Sprite 이미지를 사용하면 하나의 이미지만 로드하기 때문에 http request 수가 줄어들고 더 효율적으로 이미지를 로딩할 수 있음. 사용시에는 backround-position property 를 사용해서 위치를 불러옴.

## 2018.02.11

### image src 태그에 허용되는 글자

* encodeURIComponent 에서 예외로 처리하는 글자, 즉 알파벳, 0~9 의 숫자, - \_ . ! ~ \* '()를 제외하고는 모든 글자가 이미지 태그에 들어가면 이미지가 깨진다.

다만 이미지를 요청할 때는 encodeURI 함수를 이용해서 한글이나 공백같은 문자를 처리해줘야 제대로 이미지를 다운로드 받을 수 있다.

## 2018.02.13

### AWS 람다 간헐적 타임아웃 현상 발생

* aligo 서비스를 aws lambda 를 이용해 사용하던 중 간헐적으로 timeout error 가 발생하기 시작했다.

이 문제에 대해서 aws forum 에 대한 글도 있었다.

[AWS Forum - 504 errors - Endpoint request timed out](https://forums.aws.amazon.com/thread.jspa?threadID=216006)

문제에 대해서 뚜렷한 해결책은 없었고 다만 람다가 시동을 하기 위한 시간이 필요하다는 답변과 timeout 시간을 늘려보라는 정도의 조언밖에 없었다.

해결:

람다는 기본적으로 함수 컨테이너이다.

그리고 이 함수 컨테이너는 일정 시간(일반적으로 40 분 가량) 호출되지 않으면 'cold' 상태로 접어드는데, 만약 직접적으로 lambda 와 연결되지 않은 경우 가령 vpc 를 통해 연결되는 경우 cold 상태에서 hot 상태로 넘어가는데 추가적인 시간이 발생한다.

[fix lambda cold behavior - for serverless framework](https://serverless.com/blog/keep-your-lambdas-warm/)

[resolving cold start in aws lambda](https://medium.com/@lakshmanLD/resolving-cold-start%EF%B8%8F-in-aws-lambda-804512ca9b61)

그러나 만약 람다를 주기적으로 호출해준다면 이러한 현상을 방지할 수 있다. 그러기 위해서는 먼저 람다를 호출하는 다른 람다를 생성할 필요가 있다.

[람다를 호출하는 다른 람다 생성하기](https://stackoverflow.com/questions/31714788/can-an-aws-lambda-function-call-another)를 참조.

기본적인 개념은 aws-sdk 를 이용해서 functionName 과 payload(이벤트 인자)를 넘겨주고 실행값을 받아오는 것.

이 때 람다를 호출하는 다른 람다를 실행하려면 람다실행권한이 필요한데 이는 IAM 에서 람다정책(AWSLambdaRole)을 추가해주면 된다.

이 과정없이 람다를 호출하면 다른 람다를 호출할 권한이 없다는 에러메시지를 받게 된다.

이제 람다를 호출하는 다른 람다를 생성했으니 주기적으로 이 람다를 호출하는 이벤트를 트리거해보자.

aws 는 기본적으로 scheduled event 를 지원한다. [참조](https://docs.aws.amazon.com/lambda/latest/dg/with-scheduled-events.html)

그리고 이벤트를 생성하는 법 또한 상당히 간단한데 람다 콘솔에서 [트리거추가]-[CloudWatch Events]탭을 누르고

간단한 이름/규칙 설명을 적어준 후 예약표현식만 기입하면 된다.

[Rate / Cron expression examples](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-cron.html)

예를 들어 매 20 분마다 실행되는 람다를 만들고 싶다면

rate(20 minutes) 혹은 cron(0/20 \* \* _ ? _) 이런 식으로 기입하면 된다.

여기까지 완료되었다면 이제 cold 람다가 hot 람다가 될 때까지 기다리느라 timeout 될 걱정없이 람다를 사용할 수 있다.

람다는 기본적으로 요금 자체가 저렴하므로 추가 호출에 대한 비용문제는 거의 걱정할 필요가 없다고 봐도 무방하다.

### 추가단말서비스 경고 메시지 우회

* U+, KT 등 통신사에서 인터넷을 특정 대수 이상 연결하려면 추가단말서비스를 신청해야한다는 경고 페이지에 대한 우회법.

1.  기본적으로 이들 통신사는 모바일 트래픽에 대해서는 검열을 실시하지 않는다.

2.  그러므로 browser 의 user-agent 를 mobile browser 로 바꿔주면 검열이 원천 차단된다.

3.  크롬 확장 프로그램 중 [user-agent-swithcer for chrome]을 설치하고 아무 모바일 브라우저나 선택해준다. 석세스!

### 바벨 알아보기

* [바벨의 preset 과 plugin 이 어떤 것인가를 설명한 글](https://www.fullstackreact.com/articles/what-are-babel-plugins-and-presets/)

* [바벨 공식 문서 - env preset](https://babeljs.io/docs/plugins/preset-env/)

* [바벨 공식 문서 - stage-3 preset](https://babeljs.io/docs/plugins/preset-stage-3/)

요약: env preset 은 별다른 설정이 없다면 공식 지원되는 es5, 6, 7 등에 따라 트랜스파일을 지원하며, 특정 브라우저 환경 혹은 노드 환경에 맞게 설정해줄 경우 그에 맞게 트랜스파일한다.

```json
{
  "presets": [
    [
      "env",
      {
        "targets": {
          "browsers": ["last 2 versions", "safari >= 7"]
        }
      }
    ]
  ]
}
```

위와 같을 경우 사파리는 7 이상, 다른 브라우저는 최신 2 버전을 지원.

두 번째로 stage-x 는 실험적인 기능, 즉 공식 es 스펙에 들어가지 못한 기능을 지원하는 플러그인이다.

stage-0 부터 stage-4 까지 있으며, stage-4 는 이미 스펙에 들어간 기능, stage-0 는 제안 단계에 머무는 기능이다.

일반적으로 stage-3 이상을 사용할 것을 권장한다.

현재(2018.02.13) stage-3 에 포함된 기능은

```
  transform-object-rest-spread
  transform-async-generator-functions // async/await과 generator를 함께 쓰는 경우
```

의 두 가지다.

그러므로 만약 코드베이스에서 async generator 나 ...(spread operator)를 쓸 일이 있다면 필수 설치.

## 2018.02.20

### AWS NAT gateway 와 NAT instance

NAT gateway 는 aws 에서 관리되는 "플랫폼"이며, 좀더비싸고(한 달에 약 42 달러) 속도가 빠르다. 또 서버를 따로 관리해줄 필요가 없다.

반면 NAT instance 는 NAT 기능을 하는 서버 인스턴스다.

굉장히 저렴하지만(약 NAT gateway 의 1/10) OS update 등은 따로 해주어야한다.

속도나 여러 요청을 처리하는데는 문제가 있을 수 있다.

기능(private subnet 에서 이뤄지는 요청을 igw 에 전달)은 둘 모두 동일하다.

### Invalid end of json input / unexpected token at json

SendGrid 서비스를 사용하던 중 발생했는데

이는 response 객체가 202 반응에 따라 빈 응답을 내어놓았기 때문이다.

아무것도 돌아오지 않았기 때문에 이를 json() 형태로 변환하던 중 에러가 발생한 것.

참고로 sendGrid 의 202 에러는 개발진들의 계정 제한에 의해 발생하는 것 같다.

[참조](https://stackoverflow.com/questions/42214048/sendgrid-returns-202-but-doesnt-send-email)

### JSON invalid characters

[JSON Invalid characters](http://support.backendless.com/topic/json-invalid-characters)

일반적으로 "이나 ' 혹은 \n 등의 특수문자는 escape 처리(\ 추가) 되어야한다.

### node test 환경에서 axios network error

jest 는 package.json 에서 테스트 환경이 node 라고 명시되어야 제대로 작동한다.

[참고](https://stackoverflow.com/questions/42677387/jest-returns-network-error-when-doing-an-authenticated-request-with-axios/44366115#44366115)

## 2018.02.22

### AWS 람다 간헐적 timeout error 해결

람다의 cold start 가 게속 trigger 되는 이유를 찾아 해메다 의외로 간단한 해결책을 찾았다.

알고보니 람다의 VPC 설정이 잘못된 것.

```
  AWS Lambda uses the VPC information you provide to set up ENIs that allow your Lambda function to access VPC resources. Each ENI is assigned a private IP address from the IP address range within the Subnets you specify, but is not assigned any public IP addresses. Therefore, if your Lambda function requires Internet access (for example, to access AWS services that don't have VPC endpoints, such as Amazon Kinesis), you can configure a NAT instance inside your VPC or you can use the Amazon VPC NAT gateway. For more information, see NAT Gateways in the Amazon VPC User Guide. You cannot use an Internet gateway attached to your VPC, since that requires the ENI to have public IP addresses.

  Important
  If your Lambda function needs Internet access, do not attach it to a public subnet or to a private subnet without Internet access. Instead, attach it only to private subnets with Internet access through a NAT instance or an Amazon VPC NAT gateway.
```

예부터 도큐먼트 잘 읽으라는 말이 괜히 있는게 아니다...

아래 중요 부분을 살펴보면 람다가 인터넷 접속이 필요하다면 "절대" 퍼블릭 서브넷이나 인터넷 접속이 불가능한 프라이빗 서브넷에 연결하지 말라고 되어 있다.

그러나 나는 이것을 간과하고 VPC 설정에서 퍼블릭/프라이빗 서브넷을 같이 집어넣는 우를 범햇다.

어떤 시간대에는 되고 또 어떤 시간대에는 되지 않던 현상은 바로 이 때문이다.

람다가 어떤 서브넷을 선택해서 요청을 보내느냐에따라 요청이 timeout 되거나 잘 되거나 했던 것.

람다는 "무조건" 프라이빗 서브넷에만 연결되어 있어야한다.

### Error: Max redirects exceeded

axios 로 이미지에 대한 get 요청을 보내던 중 발생했다.

문제행동: Error: Max redirects exceeded 라는 메시지와 함께 데이터가 돌아오지 않음

[참조](https://productforums.google.com/forum/#!topic/webmasters/xPfb4Sbf_bs)

내 경우 요청 url 에 www 가 빠져있었음.

예를 들어 https://xxx.com/은 에러를 발생시킬 수 있음.

https://www.xxx.com은 에러없이 요청이 원활히 이뤄졌음.

이는 www 가 없는 url 의 경우 redirect 를 시도하기 때문인듯함.

답변 원문

```
  If enter http://theCondo.rent into http://www.redirect-checker.org/

  it shows that redirects to http://www.thecondo.rent/ - which redirects to https://www.thecondo.rent/, which THEN loops.

  https://theCondo.rent/ (httpS and NON-www) doesn't loop.


  Its https://www.theCondo.rent (httpS and WWW) that DOES loop.
```

## 2018.02.23

### AWS api gateway 에서 api 호출 시 { message: "Forbidden" / "Missing authentication token" } 발생

api gateway 에서 api 호출 시 저런 메시지가 발생하면 api key 가 제대로 발생되지 않았다는 증거다.

그리고 다른 하나의 가능성은

![사진](./images/today-i-learned/api-gateway-error-solution.png)

그래서 API gateway 좌측 사용량 계획에서 미리 생성해둔 api key 를 적절한 스테이지에 연결해주고,

x-api-key 헤더를 넣어주었더니 정상적으로 요청이 되었다.

[참고](https://stackoverflow.com/questions/40988051/getting-message-forbidden-reply-from-aws-api-gateway)

추가 만약에 { message: "Missing authentication token" } 이 발생할 경우

[참고](http://www.awslessons.com/2017/aws-api-gateway-missing-authentication-token/)

그리고 만약에 api gateway 에 어떤 수정을 가했다면 무조건 다시 배포를 해야한다.

설정이 저장될 수 있게 말이다.

## 2018.03.01

### sort after populate

populate 에 옵션을 주면 간단하게 정렬할 수 있다.

```js
Group.find({}).populate({
  path: 'Members',
  options: { sort: { created_at: -1 } },
})
```

[참고](https://stackoverflow.com/questions/16352768/how-to-sort-a-populated-document-in-find-request)

### stay animated state

css 의 animation 중 timing 부분을 forwards 로 해주면 마지막 상태를 지속한다.

[참고](https://stackoverflow.com/questions/12991164/maintaining-the-final-state-at-end-of-a-css3-animation)

### 좀 더 부드러운 전환 효과

기본적으로 width 나 height 혹은 포지션을 animate 시키면 부드러운 모션이 나오지 않는다.

대신 transform: translateY(2px)을 사용하자.

[mdn tranform](https://developer.mozilla.org/ko/docs/Web/CSS/transform)

## 2018. 03. 03 토요일

### Docker / Docker-compose 설정 관련

기본적으로 docker-compose 시 mongodb 는 localhost 가 아닌 다른 호스트(도커 컨테이너)에 바인딩되어있기 때문에 mongo uri 에 localhost 가 들어가면 작동이 안된다. 그러므로

```yaml
  version: '3'
  services:
    allhumor:
      container_name: allhumor
      restart: always
      build: .
      image: 1227800d434b
      ports:
        - "3000:3000"
      environment:
        - NODE_ENV=development
      volumes:
        - .:/usr/src/app
        - /usr/src/app/node_modules
      depends_on:
        - db
      links:
        - db
      tty: true
  db:
    image: mongo:latest
    volumes:
      - ./data:/data/db
    ports:
      - "27017:27017"
```

저런 식으로 yml 파일이 구성되어 있다면 db 라고 되어 있는 부분을 주목하자.

mongo uri 를 쓸 때 mongo//db:27017/allhumor 이런 식으로 사용하면

Network Error 를 피할 수 있다.

그리고 image 부분에 docker file 로 빌드한 이미지를 넣어줘야한다.

만약 node 이미지나 다른 이미지를 넣어주면 아예 작동하지 않고

server exited with code 0 이런 식으로 작업이 종료되어 버린다.

다음으로 Dockerfile 적는 방법

FROM 이미지(node, alphine)

RUN 커맨드
COPY 기본적으로 도커는 작업 디렉토리에 있는 파일들을 도커 컨테이너에 옮기는 개념이기 때문에 copy 작업을 통해 package.json 과 다른 파일들을 복사한다.

그 뒤에 RUN npm install 을 통해 필요한 디펜던시를 모두 다운로드 받고

EXPOSE 3030(포트명)

CMD ["npm", "start"] 쌍따옴표를 안쓰면 ./bin/sh ['npm] not found 에러가 발생한다.

### docker remove all unnamed images

```bash
docker rmi -f $(docker images | grep "<none>" | awk "{print \$3}")
```

### docker-compose: no such image

docker-compose 는 한 번 빌드된 이미지를 caching 하기 때문에 다시 up 커맨드를 부르면 caching 된 이미지를 기반으로 빌드한다.

그러므로 모든 이미지를 먼저 지운 다음 다른 이미지를 불러와야 한다.

```bash
  docker-compose rm
```

그리고 docker-compose 에 이미지 파일을 명시할 때는 태그 네임 혹은 이미지 ID 를 넣으면 된다.

```bash
  docker build . -t TAG_NAME

  // -t flag는 태그네임 지정
```

그리고

```yml
  # 이런 식으로 tag name을 사용하거나
  allhumor:
    container_name: allhumor
    image: allhumor:latest

  # 이런 식으로 image Id를 사용할 수 있다.
  allhumor:
    container_name: allhumor
    image: 9c76be05b83f
```

## 2018. 03. 04 토요일

### pm2 npm start 로 앱 실행하기

```bash
  pm2 start npm -- start
```

-- 다음에 한 칸 띄우고 start 를 써야됨.

### ec2 인스턴스에서 node/mongo app 배포하기

중요 waypoint

1.  ec2 instance 에 nginx/node/mongodb 설정하기

먼저 sudo apt-get update && sudo apt-get upgrade -y 명령어를 통해 ubuntu 를 최신버전으로 업그레이드.

만약 보라색 바탕의 안내창이 나오면

```bash
install the package maintanier\'s version (첫 번째 옵션)을 눌러줌
```

nginx 설정하기

```bash
// 설치
sudo apt-get install nginx -y

// 상태 확인
sudo systemctl status nginx

// nginx 시작
sudo systemctl start nginx

// 시작할 시 바로 동작할 수 있게 함.
sudo systemctl enable nginx
```

node js 설정하기

nvm(노드 버전 매니져)을 이용하면 손쉽게 노드 버전을 관리할 수 있음.

```bash
// 업데이트
sudo apt-get update

// curl을 이용 install.sh를 다운
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

// 아래 커맨드를 입력해야 nvm 커맨드를 쉘에 쓸 수 있음
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"

// 노드 인스톨(최신버전)
nvm install node

// 노드 사용
nvm use node
```

mongodb 설정

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927

echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list

sudo apt-get update

sudo apt-get install -y mongodb-org

sudo service mongod start

// mongod 상태 확인
sudo service mongod status

// 서버 로그인 시마다 자동으로 mongod 실행
sudo systemctl enable mongod && sudo systemctl start mongod

// 이제 유저를 생성하고 보안 접속을 설정
mongo

use [db 이름]

db.createUser({
  user: "USER_NAME",
  pwd: "PASSWORD"
  roles: [
    {
      role: "readAnyDatabase",
      db: "admin"
    },
    "readWrite"
  ]
})

db.auth("USER_NAME", "PASSWORD")

// 몽고디비 설정 바꾸기
sudo nano /etc/mongodb.conf

이 중 auth=true로 된 부분을 주석 해제처리한다.
```

git 공개키 설정

```bash
기본적으로 공개키는 구비되어 있지 않다. 그러므로 ssh-keygen 을 통해 만들어야 한다.

cd ~/.ssh

ssh-keygen

// 키 보기
cat id_rsa.pub

이제 키를 git 저장소의 settings-deploy keys에 추가해준다.

이후에는 git clone [repo name]명령어를 사용할 수 있다.
```

pm2 설정

```bash
npm install pm2 -g

// npm start 스크립트 실행
pm2 start npm -- start

// 현재 실행 중인 앱 리스트
pm2 list

// 앱 제거
pm2 delete [app]
```

도메인 설정

```bash
// 기본 설정 삭제
sudo rm /etc/nginx/sites-available/default

// 새 설정 생성
sudo nano /etc/nginx/sites-available/default

server {
    listen 80;
    server_name your_domain.com www.your_domain.com;
    location / {
        proxy_pass http://127.0.0.1:8080; // 포트를 헷갈리지 말고 잘 써줘야함.
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_redirect off;
     }
}

// 설정 적용
sudo systemctl reload nginx
```

## 2018. 03. 05. 월

### 도메인 연결하기

도메인을 연결하기 전에 먼저 ec2 인스턴스에 필요한 eleastic IP를 연결해놓고, 그 IP 주소를 등록하도록 하자.

이번에는 무료 도메인 연결을 시도했다. 도메인 구입은 [freenom](http://www.freenom.com/en/index.html?lang=en)에서 했고, 도메인 관리는 하단의 ```my domains```를 눌러 들어가면 된다.

![freenom](./images/today-i-learned/freenom.png)

그 다음엔 해당되는 도메인의 ```manage domain```메뉴를 누른다.

![freenom](./images/today-i-learned/freenom2.png)

상단의 네 개 메뉴 중 Manage Freenom DNS를 누른다.

![freenom](./images/today-i-learned/freenom3.png)

아래에 보면 name/target을 입력할 수 있는데,

name에는 도메인이름 ex) allhumor.ga / www.allhumor.ga
target에는 IP 주소 ex) 52.78.233.72

![freenom](./images/today-i-learned/freenom4.png)

참고로 https 설정을 위해 www 버전과 일반 버전 모두를 등록해주는 것이 좋다.

또 등록에는 시간이 조금 걸리므로 등록 후 바로 접속이 안 돼도 이상하게 생각하지 말자.

### 처음부터 끝까지 따라하는 aws ec2 deploy and nigx settup

[medium link](https://medium.com/@Keithweaver_/setting-up-mern-stack-on-aws-ec2-6dc599be4737)

## 2018. 03. 07. 수

### 테스트 환경 setup 하기

현재 (2018. 03. 07. 기준) jest-cli@20.0.4 버전을 사용하는게 좋다.

```bash
  yarn add --dev jest-cli@20.0.4 enzyme enzyme-to-json
```

snapshotSerializers 옵션은 jest snapshot을 찍을 경우 간략하게 dom 정보만 보여주도록 바꿈.

collectCoverageFrom 옵션은 테스트 커버리지 검사 시 포함/미포함 항목을 설정함.

앞에 !싸인을 붙이면 테스트 커버리지 검사에서 제외함

```json
  "jest": {
    "snapshotSerializers": [
      "enzyme-to-json/serializer"
    ],
    "collectCoverageFrom": [
      "src/**/*.(js|jsx)",
      "!src/index.js",
      "!src/setupTests.js"
    ]
  }
```

마지막으로 src 폴더에 setupTest.js를 추가해주자.

```js
  import { configure } from 'enzyme'
  import Adapter from 'enzyme-adapter-react-16'

  configure({ adapter: new Adapter(), disableLifecycleMethods: true })
```

```bash
  // 테스트 커맨드
  yarn jest --watchAll

  // 커버리지 확인
  yarn jest -- --coverage

  // 스냅샷 업데이트
  u
```

기본적인 use case

```js
  import React from 'react'
  import { shallow } from 'enzyme'

  const props = { headerText: 'hi' }
  const app = shallow(<App {...props} />)

  describe('App', () => {
    // 스냅샷 테스트
    it('renders properly', () => {
      expect(app).toMatchSnapshot()
    })

    describe('when clicked', () => {
      // 동작을 simulate
      beforeEach(() => {
        app.find('button').simulate('click')
      })

      it('renders text', () => {
        expect(app.find('h1').text()).toEqual('sample text')
      })

      it('creates empty input field', () => {
        // Input -> React.Element.
        // 만약 connected component 라면?
        // Connect(App) 이런 식으로 검사
        expect(app.find('Input').exists()).toBe(true)
      })
    })
  })
```

### docker 명령어 / 플래그 해설

```bash
  // 이미지 제거
  docker rmi IMAGE_NAME or ID

  // kill and remove docker container
  // 여러 개를 제거할 때는 공백으로 구분함.
  docker rm -f CONTAINER_ID or NAME

  // -d 백그라운드 앱으로 실행
  // -e 환경변수 설정
  // -p 포트 바인딩
  // --name 컨테이너명
  // server: 이미지 명
  docker run -d -e "NODE_ENV=development" -p 3001:3030 --name server server
```

### Redis use cases

Redis는 API 기반의 마이크로서비스를 사용할 경우 거의 필수적으로 사용된다.

그 이유는 redis가 in-memory 기반의 데이터저장소 이기 때문에 굉장히 빠른 반응속도를 보여주기 때문이다.

Redis의 다양한 use case는 아래 블로그의 글처럼 정리된다.

[참고](https://www.objectrocket.com/blog/how-to/top-5-redis-use-cases)

```text
  1. session cache
  2. full page cache
  3. queues
  4. leaderboards/counting
  5. pub/sub
```

그 중에서도 application의 caching layer로써 사용될 수 있는데, 이 경우 직접 api를 호출하는 것보다 탁월한 반응속도를 보여준다.

[Redis로 caching layer만들기 example](https://coligo.io/nodejs-api-redis-cache/)

차이가 적게는 3배에서 많게는 10배까지 난다. API caching은 필수적인듯.

### Redis - docker compose에서 설정하기

[참고](https://stackoverflow.com/questions/41427756/error-redis-connection-to-127-0-0-16379-failed-connect-econnrefused-127-0-0/41428342#41428342)

기본적으로 redis는 redis-server 서비스를 호출함으로써 이용할 수 있다. 이 경우 호스트는 당연히 로컬호스트(127.0.0.1), 포트는 기본 포트인 6379에 연결된다.

그러나 도커는 컨테이너 환경이다. 그러므로 호스트가 달라진다.

그래서 만약 redis.conf 설정으로 호스트나 엔트리포인트를 설정하지 않은 경우 또는 network 설정을 통해 redis 와 앱을 묶어주지 않은 경우 ```connection ERROR 127.0.0.1``` 을 볼 수 있다.

도커 컨테이너는 로컬호스트가 아니다라는 개념이 중요하다.

추가)
  docker-compose 2.x 버전 이후로 links는 deprecated 되었으며 network를 통해 연결하는 방법이 더 타당하다.

  network 설정 예시)

```yaml
version: "3.5"
services:
  server1:
    container_name: server1
    build: .
    ports:
      - "3001:3030"
    environment:
      - MESSAGE=halo
      - HOST=mongo
      - DB=test
      - REDIS_PORT=6379
      - REDIS_HOST=redis
    volumes:
      - ./:/usr/src/app
      - ./node_modules:/usr/src/app/node_modules
    networks:
      - backend

  nginx:
    container_name: nginx
    restart: always
    build: ./nginx/
    ports:
      - "3030:80"
    networks:
      - backend

  mongodb:
    container_name: mongo
    image: mvertes/alpine-mongo
    ports:
      - "27017:27017"
    networks:
      - backend

  redis:
    container_name: redis
    image: redis:4.0.8-alpine
    networks:
      - backend

networks:
  backend:
    driver: bridge
```

모두 같은 backend 네트워크로 연결되어 있으며, 직접 서비스를 연결할 때는(mongo 디비 연결이나 redis 클라이언트 연결) 서비스의 이름(mongodb/redis)이나 별칭(컨테이너 명)을 호스트 명으로 넘겨줘야한다.

ex)
  ```text
    mongodb://HOST:27017/dbname
    이 경우 HOST는 서비스 명인 mongodb거나
    컨테이너명이 정해져있는 경우 컨테이너 명인 mongo임
  ```


### docker compose volume

볼륨은 방대한 파일을 어떤 식으로 처리할까라는 고민에 대한 답을 담고 있다. 일반적으로 도커 컨테이너는 일회성이기 때문에 컨터이너 간에 파일을 공유한다거나 하는 일은 불가능하다.

그러나 적절히 volume 설정을 해준다면 호스트 컴퓨터의 path에 파일을 저장시켜놓고 컨테이너 간에 공유를 할 수 있다.

이는 쉽게 생각하면 창고 개념으로 볼 수 있다. 창고에 자료를 넣어두면 아무나 가져가서 볼 수 있듯이 도커도 볼륨을 설정해두면 볼륨에 해당하는 파일은 컨테이너 간에 공유도 가능하고, 호스트 컴퓨터의 공간에 저장된다.

### docker image 용량 줄이기

기본적으로 docker image들은 용량이 제법 큰 편이다.

최소 300 ~ 600mb 쯤 나가는 이미지들이 대다수다.

그러나 alpine 버전을 사용하면 1/3 ~ 1/5의 용량으로도 해당 이미지를 사용할 수 있다.

