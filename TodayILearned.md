# Today I Learned (2018-01-29 ~ 2018. 03. 24)

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

1. ec2 instance 에 nginx/node/mongodb 설정하기

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

도메인을 연결하기 전에 먼저 ec2 인스턴스에 필요한 eleastic IP 를 연결해놓고, 그 IP 주소를 등록하도록 하자.

이번에는 무료 도메인 연결을 시도했다. 도메인 구입은 [freenom](http://www.freenom.com/en/index.html?lang=en)에서 했고, 도메인 관리는 하단의 `my domains`를 눌러 들어가면 된다.

![freenom](./images/today-i-learned/freenom.png)

그 다음엔 해당되는 도메인의 `manage domain`메뉴를 누른다.

![freenom](./images/today-i-learned/freenom2.png)

상단의 네 개 메뉴 중 Manage Freenom DNS 를 누른다.

![freenom](./images/today-i-learned/freenom3.png)

아래에 보면 name/target 을 입력할 수 있는데,

name 에는 도메인이름 ex) allhumor.ga / www.allhumor.ga
target 에는 IP 주소 ex) 52.78.233.72

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

snapshotSerializers 옵션은 jest snapshot 을 찍을 경우 간략하게 dom 정보만 보여주도록 바꿈.

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

마지막으로 src 폴더에 setupTest.js 를 추가해주자.

```js
import { configure } from 'enzyme'
import Adapter from 'enzyme-adapter-react-16'

configure({ adapter: new Adapter(), disableLifecycleMethods: true })
```

만약 typescript를 사용 중이라면 여기에다가 package.json의 jest 옵션에

```json
"setupTestFrameworkScriptFile": "<rootDir>/src/setupTests.ts"
```

와 같이 추가해준다.

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

Redis 는 API 기반의 마이크로서비스를 사용할 경우 거의 필수적으로 사용된다.

그 이유는 redis 가 in-memory 기반의 데이터저장소 이기 때문에 굉장히 빠른 반응속도를 보여주기 때문이다.

Redis 의 다양한 use case 는 아래 블로그의 글처럼 정리된다.

[참고](https://www.objectrocket.com/blog/how-to/top-5-redis-use-cases)

```text
  1. session cache
  2. full page cache
  3. queues
  4. leaderboards/counting
  5. pub/sub
```

그 중에서도 application 의 caching layer 로써 사용될 수 있는데, 이 경우 직접 api 를 호출하는 것보다 탁월한 반응속도를 보여준다.

[Redis 로 caching layer 만들기 example](https://coligo.io/nodejs-api-redis-cache/)

차이가 적게는 3 배에서 많게는 10 배까지 난다. API caching 은 필수적인듯.

### Redis - docker compose 에서 설정하기

[참고](https://stackoverflow.com/questions/41427756/error-redis-connection-to-127-0-0-16379-failed-connect-econnrefused-127-0-0/41428342#41428342)

기본적으로 redis 는 redis-server 서비스를 호출함으로써 이용할 수 있다. 이 경우 호스트는 당연히 로컬호스트(127.0.0.1), 포트는 기본 포트인 6379 에 연결된다.

그러나 도커는 컨테이너 환경이다. 그러므로 호스트가 달라진다.

그래서 만약 redis.conf 설정으로 호스트나 엔트리포인트를 설정하지 않은 경우 또는 network 설정을 통해 redis 와 앱을 묶어주지 않은 경우 `connection ERROR 127.0.0.1` 을 볼 수 있다.

도커 컨테이너는 로컬호스트가 아니다라는 개념이 중요하다.

추가)
docker-compose 2.x 버전 이후로 links 는 deprecated 되었으며 network 를 통해 연결하는 방법이 더 타당하다.

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

그러나 적절히 volume 설정을 해준다면 호스트 컴퓨터의 path 에 파일을 저장시켜놓고 컨테이너 간에 공유를 할 수 있다.

이는 쉽게 생각하면 창고 개념으로 볼 수 있다. 창고에 자료를 넣어두면 아무나 가져가서 볼 수 있듯이 도커도 볼륨을 설정해두면 볼륨에 해당하는 파일은 컨테이너 간에 공유도 가능하고, 호스트 컴퓨터의 공간에 저장된다.

### docker image 용량 줄이기

기본적으로 docker image 들은 용량이 제법 큰 편이다.

최소 300 ~ 600mb 쯤 나가는 이미지들이 대다수다.

그러나 alpine 버전을 사용하면 1/3 ~ 1/5 의 용량으로도 해당 이미지를 사용할 수 있다.

## 2018. 03. 10. 토

### mongodb replica set 설정하기

먼저 docker-compose.yml 파일에 두 개의 secondary mongod 인스턴스를 추가해준다. 이 때 컨테이너 포트는 다르게 바인딩 해준다. (27017:27017, 27018:27017)

다음으로 `docker exec -it mongo<컨테이너명> mongo` 명령어를 통해 primary mongod 인스턴스의 mongo shell 에 접속한다.

이제 replica set 을 생성하자.

```js
// 시작
rs.initiate()

// secondary instance 추가
rs.add('<hostname or container name>:<port>')

// 상태 확인
rs.status()
```

만약 상태를 확인했을 때 host 명이 localhost 로 되어있다면 적절한 컨테이너 명으로 바꿔줘야한다.

```bash
cfg = rs.conf()
cfg.members[1].host = "mongodb1.example.net:27017"
rs.reconfig(cfg)
```

이제 각 인스턴스의 커맨드는

```bash
mongod --smallfiles --replSet <SET이름>
```

이 되어야한다.

다음 단계는 keyfile 을 이용해서 access control 을 해야한다.

먼저 keyfile 을 생성해주자.

```bash
openssl rand -base64 756 > <path-to-keyfile>
chmod 400 <path-to-keyfile>
```

다음으로 각 인스턴스의 볼륨 옵션에 이 키파일을 추가해주자.

```yml
secondary:
  container_name: secondary
  image: mvertes/alpine-mongo
  ports:
    - "27018:27017"
  volumes:
    - mongo1:/data/db
    # 이런 식으로 keyfile을 넘겨준다.
    - ./keyfile:/opts/mongors/keyfile
  # keyfile 옵션을 추가
  command: mongod --smallfiles --replSet test --keyFile /opts/mongors/keyfile --auth
  networks:
    - backend
```

이제 primary 에 localhost(no auth 상태)로 mongo shell 에 접속한다.

다음으로 admin 유저를 생성한다.

```js
admin = db.getSiblingDB('admin')
admin.createUser({
  user: 'fred',
  pwd: 'changeme1',
  roles: [{ role: 'userAdminAnyDatabase', db: 'admin' }],
})
```

이제 몽고쉘에 들어가 인증하려면

```js
db.getSiblingDB('admin').auth('fred', 'changeme1')
```

끝!

### non primary instance 에서 query 하기(not master 에러)

replset 에서 PRIMARY 가 아닌 다른 mongod 인스턴스에서 query 를 시도하면 다음과 같은 에러가 발생한다.

```bash
test:SECONDARY> db.users.find()
Error: error: {
        "operationTime" : Timestamp(1520689840, 1),
        "ok" : 0,
        "errmsg" : "not master and slaveOk=false",
        "code" : 13435,
        "codeName" : "NotMasterNoSlaveOk",
        "$clusterTime" : {
                "clusterTime" : Timestamp(1520689840, 1),
                "signature" : {
                        "hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
                        "keyId" : NumberLong(0)
                }
        }
}
```

이 경우

```bash
rs.slaveOk() 를 통해서 slave에서 query를 할 수 있다.

혹은 PRIMARY instance의 hostaname을 알아보려면

rs.status() 를 실행한다.

그리고 terminal에서

docker exec -it container_name mongo --host container_name을 시도
```

## 2018. 03. 12. 월

### Elasticsearch: "No living connections" 에러 해결

docker 환경에서 elasticsearch 를 사용하기 위해 아래와 같이 설정을 세팅했다.

```yml
version: "3.5"
services:
  server1:
    container_name: server1
    build: .
    ports:
      - "3001:3030"
    environment:
      ...
      - ES_HOST=elasticsearch
    volumes:
      - ./:/usr/src/app
      - ./node_modules:/usr/src/app/node_modules
    networks:
      - backend

  elasticsearch:
    container_name: elasticsearch
    image: docker.elastic.co/elasticsearch/elasticsearch:6.2.2
    volumes:
      - esdata:/usr/elasticsearch/data
    environment:
      - bootstrap.memory_lock=true
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
      - discovery.type=single-node
    ports:
      - "9300:9300"
      - "9200:9200"
    ulimits:
      memlock:
        soft: -1
        hard: -1

networks:
  backend:
    driver: bridge

volumes:
  esdata:
```

그러나 지속적으로 elasticsearch 에서는 No living connection 에러만이 발생했다.

그래서 깃헙 등을 돌아다니며 몇 가지 솔루션을 제안받았지만 실패했다.

그러다 로컬에서 다운받고 실행을 시키니 정상적으로 작동하는 것이었다.

connection 이 성립하지 못하는 것은 host 나 port 와 관련이 있을 것같아서

```bash
docker inspect <container_name>
```

아래 명령어를 입력하니

```json
[
    {
        "Id": "62a0886daf9965fc59c592c8c5892405a1ac1216a86bf00babaae9761020f816",
        "Created": "2018-03-11T15:01:40.471293562Z",
        "Path": "/usr/local/bin/docker-entrypoint.sh",
        "Args": [
            "eswrapper"
        ],
        ...
            "Networks": {
                "dockercomposeexample_default": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "elasticsearch",
                        "62a0886daf99"
                    ],
                    "NetworkID": "4b88c92cdd8ba431fb81e0e6d1953dc0200770c0372f0b72d27ad0a03c2ec300",
                    "EndpointID": "102654655a687c055494120323e131a45796505347beb569ef0178233ac54099",
                    "Gateway": "172.24.0.1", #gateway
                    "IPAddress": "172.24.0.2", #privateIP
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:18:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

Network 항목에서 Gateway 와 IPAddress 항목이 보였다.

나는 IPAddress 를 host 로 설정했다. 그러나 마찬가지로 작동하지 않았다.

그래서 host 값을 172.24.0.1 즉 gateway 의 IP 로 바꾸었더니 잘 작동했다.

docker container 나 daemon 이 어떤 식으로 작동하는지 파악할 필요가 있을 것 같다.

[stackoverflow 질문](https://stackoverflow.com/questions/38467036/no-living-connections-error-while-elasticsearch-connections-in-nodejs/49229143#49229143)

## 2018. 03. 17 토

### 맥용 엑셀 .csv 한글 깨짐 현상

맥용 엑셀에서 한글 파일을 열 경우 일본어로 깨져보이는 경우가 있다.

이는 기본 인코딩이 utf-8 이 아니기 때문이며, 이 경우 엑셀 상단의 [데이터]-[텍스트에서]

를 선택하고, Mac(한글) 옵션을 선택하면 미리보기에서 정상적으로 출력된다.

그러나 이는 문서를 읽을 때에만 해당되는 것으로, 만약 그 상태에서 다시 .csv 확장자로 문서를 저장할 경우 모든 non-ascii 글자가 \_(underscore)로 표시된다.

이 때 해결 방법은 파일을 UTF-16 유니코드 텍스트로 저장한 뒤 확장자를 .csv 로 다시 바꿔주는 방법이다. 이렇게 하면 파일을 읽을 때, 쓸 때 모두 안전하게 non-ascii 글자를 사용할 수 있다.

## 2018. 03. 18 일

### �(question mark inside black cube/diamond)

위 글자가 발생하는 이유는 어떤 글자를 UTF-8 로 인코딩하려다가 실패할 경우 강제로 저 글자로 대체하기 때문에 생겨난다.

[참고](https://discuss.elastic.co/t/logstash-invalid-character-for-utf-16-unicode-encoding/56702/5)

```text
// 답변 원문
The question-mark-in-black-diamond character is a replacement character that is used when the UTF16 -> UTF8 character conversion fails.

This piece of config codec => plain { charset => "UTF-16" } says to Logstash "Treat all text as UTF16 and convert it to UTF8"

There may be some illegal surrogates http://unicode.org/faq/utf_bom.html#utf16-716
or maybe the charset conversion library we use does not deal with noncharacters http://www.unicode.org/faq/private_use.html#noncharacters15 very well.
```

## 2018. 03. 19. 월

### logstash에 저장한 파일이 logging되지 않아요 ㅜ

logstash는 event를 통해 읽어들인다. 이 말은 파일이 변경되거나 혹은 처음 logstash에 저장되는 것이 아닌 이상 새로 읽어들이지 않는다는 것이다. 그러므로 파일이름을 바꾸거나 아니면 파일을 새로 수정해야 다시 읽어들인다. 또한 logstash는 한 번 실행되고 종료되지 않는다. 지속적으로 실행된 상태를 유지한다.

### Logstash csv 파일 다루기

맥/윈도우 액셀 모두 csv 파일을 기본적으로 저장할 경우, \_\_\_ 와 같이 언더스코어만 계속 보이거나 한자/일본어만 계속해서 보이는 상황이 일어난다.

이는 인코딩 문제의 영향으로 다른이름으로 저장 - UTF-16 텍스트로 저장을 통해 언더스코어로 저장되는 일을 방지할 수 있다.

그러나 logstash 의 codec 을 UTF-16 으로 지정하더라도 \\u000 와 같은 텍스트가 뜨는 에러가 발생한다.

또 다른 codec 의 인코딩을 다른 것으로 지정하더라도 � 와 같이 utf-8 변환이 실패했을 때 나오는 텍스트가 나오기 때문에 찾아낸 유일한 방법은

```text
1. 엑셀 위쪽 탭에서 [데이터]-[텍스트에서 가져오기]
2. utf-16 텍스트로 저장하기
3. 윈도우에서 엑셀 파일을 한 번 연다.(이 때 저장을 통해 cp949 인코딩으로 저장된다.)
4. 윈도우 노트패드를 통해 연다. (이때 인코딩은 ANSI)로 지정.
5. utf-8 형식으로 저장한다.
```

3 단계에서 엑셀 파일을 굳이 열어서 한 번 더 저장해주는 이유는 utf-16 텍스트로 저장한 파일을 노트패드로 열어서(이 때는 인코딩을 유니코드로 지정해야 열림) 저장할 경우 기껏 지정해놓은 delimeter(구분자)가 사라져 한 칼럼에 모든 데이터가 들어가게 되기 때문에 logstash 에서 seperator 를 지정할 수 가 없기 때문이다.

3 단계를 하고 나면 노트패드를 통해 열었을 때 ,(delimeter)가 살아있게 되어 utf-8 형식으로 바꾸더라도 제대로 칼럼이 나눠져 있다.

만약 위의 절차를 하나라도 어길 시 logstash 를 통해서 한글 데이터를 저장할 수 없다.

```conf
input {
  file {
    # path는 절대 경로를 사용하기 않으면 에러가 발생함.
    path => "/Users/leejunhyung/Downloads/auft.csv"
    start_position => "beginning" # 파일을 처음부터 읽어들인다.
    sincedb_path => "/dev/null"
  }
}

filter {
  csv {
    separator => "," # 기본 seperator
    columns => ["id", "name", "branch", "old_address", "new_address", "latitude", "longitude"]
  }
  mutate { 
    # elasticsearch에서 location은 geo_point타입이다.
    # 그리고 아래와 같이 먼저 latitude, longitude 칼럼을 float 타입으로 바꿔주고
    # 그 다음에 아래와 같이 location 아래에 위치시킨다.
    convert => { "latitude" => "float" }
    convert => { "longitude" => "float" }
    rename => [
      "latitude", 
      "[location][lat]", 
      "longitude", 
      "[location][lon]"
    ]
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "csv" # 미리 index가 생성되어 있어야 도큐먼트가 저장됨.
  }
  stdout {
    codec => rubydebug # 데이터를 json 형태의 읽기 좋은 형태로 보여줌.
  }
}
```

## 3028. 03. 20. 화

### Node.js 버전 충돌 문제 해결

Node.js를 사용하다보면 자연스럽게 여러 버전의 node.js를 다운로드 받게 됩니다.

때로는 공식 홈페이지에서 다운로드 받을 수도 있고, 맥 유저라면 homebrew를 통해 다운로드 받을 수도 있죠.

이런 상황이 지속되다보면 node --version 명령어를 쳤을 때 이전 버전이 출력되는 일이 일어나기도 합니다.

이는 여러군데서 node.js를 다운로드 받았기 때문에 발생하는 문제인데, 다음과 같이 해결할 수 있습니다.

1. Node.js 공식 홈페이지에서 다운로드 받은 경우(http://nodejs.org)

이 경우 명령어 한 방으로 정리 가능

```bash
rm -fr /usr/local/bin/{node,npm} /usr/local/lib/node_modules/
```

[참조](https://github.com/nodejs/node-v0.x-archive/issues/4058)

2. Homebrew를 통해 다운로드 받은 경우

먼저 ```brew list```를 통해 node.js를 다운로드 받았는지부터 확인하자

만약 node.js가 확인된다면 ```brew uninstall node --force```을 통해 지워주세요.

--force를 붙여야만 여러 버전의 node.js를 지울 수 있습니다.

Node.js는 보통 여러 경로를 통해서 다운로드 받을 수 있기 때문에 이처럼 버전 충돌 문제가 꽤 자주 발생합니다. 그러므로 한 경로를 통해서만 Node.js를 다운로드 받는 것이 좋습니다.

그리고 많은 경우 가장 추천드리는 방법은 nvm을 이용하는 것입니다.
nvm은 Node Version Manager의 약자로 node.js의 버전 관리를 도와주는 bash script입니다.

[링크](https://github.com/creationix/nvm)

사용법은 아래와 같습니다. (출처: nvm 공식문서)

```bash
# 설치(curl 혹은 wget을 이용)
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

# nvm 명령어 불러오기
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

만약 nvm 명령어를 정상적으로 불러올 수 없다면 직접 .bashrc를 켜서 위와 같이 추가해줍니다.

open ~/.bashrc # .bashrc 열기

위의 내용을 추가했다면 source ~/.bashrc를 통해 내용을 불러옵니다.

# node.js 설치
nvm install node # 최신버전 설치
nvm use node # 최신버전 사용

혹은

nvm install --lts # LTS(장기 지원) 버전 다운로드
nvm install 9.8.0 # 특정 버전 사용
```

마무리:

nvm 자체 버전 업데이트

```bash
set -e

cd ~/.nvm

git fetch --tags
TAG=$(git describe --tags `git rev-list --tags --max-count=1`)
echo "Checking out tag $TAG..."
git checkout "$TAG"

source ~/.nvm/nvm.sh
```

[참조](https://github.com/creationix/nvm/issues/400)

이제 즐거운 node.js 라이프를 즐겨봅시다!

### 생활코딩 리눅스 강의 중 왜 CLI 인가'?'

linux에서는 각 프로세스의 출력을 다른 프로세스의 입력으로 전환할 수 있다.

예를 들면 파일의 내용을 보여주는 cat 명령어와 특정 단어가 포함된 열을 보여주는 grep 명령어를 조합하면

```bash
# 특정 파일에서 keyword가 들어간 행만 찾을 수 있음
cat [directory] | grep [keyword] | other_commands
```

### 생활코딩 리눅스 강의 중 IO Redirection

리눅스 환경에서는 명령어 프로세스를 통해서 출력된 결과를 수동으로 ctrl c + ctrl v 하지 않더라도 파일로 볼 수 있다.

예시:

```bash
# 이 명령어는 앞선 명령어의 결과, 즉 sometext.txt를 복사해서 copy.txt 파일을 출력할 것이다.
cat sometext.txt > copy.txt
```

이러한 IO Redirection은 stdinput/stdoutput/stderror를 대상으로 한다.

즉, 어떤 프로세스의 에러 출력도 리다이렉션이 가능한 것이다.

예를 들면

```bash
rm notexists.txt
# no such file: notexists error!

rm notexists.txt 2> error.log
# 이제 error.log에 에러가 저장됩니다.

rm notexists.txt 1> result.txt 2> error.log
# 이런 식으로 결과가 있다면 result.txt에,
# 에러가 발생하면 error.log에 저장하는 것도 가능합니다.
```

+추가

만약 >> 로 리다이렉션을 하면 기본적인 행동방식이 overwrite에서 append로 바뀐다. 즉 기존 아웃풋을 덮어쓰지 않고 추가만 한다.

```bash
nano hello.txt # hello

# 결과를 덮어씀
cat hello.txt > manyhellos.txt # hello
cat hello.txt > manyhellos.txt # hello

# 이미 존재하는 결과에 덧붙임
cat hello.txt >> manyhellos.txt # hello
cat hello.txt >> manyhellos.txt # hello hello
```

번외) /dev/null => unix 계열 os의 휴지통 개념

## 2018. 03. 22 목

### 유저 디렉토리로 한 방에 이동

유닉스 계열의 운영체제는 일반적으로 /home 디렉토리 아래에 유저명의 디렉토리를 가진다.

ex) /home/user1

이 때 유저명의 디렉토리로 한 방에 이동하려면 위의 경로를 치는 것 대신 ```cd ~```을 치면 한 방에 이동할 수 있다.

## 2018. 03. 23 금

### http/2, spdy

http/1.1보다 속도 면에서 많은 성과를 이뤄냄.

주요한 특징으로는 multiplexed stream, stream prioritization, server push, header compression 등이 있으며, 성능면에서 많은 개선이 이뤄짐.

또 http/2는 https 기반에서만 작동하므로 암호화의 이점까지 가져감.

spdy: http/2 이전에 구글이 개발한 개선 프로토콜

구현

```js
import fs from 'fs'
import path from 'path'

import express from 'express'
import spdy from 'spdy'

import db from './db'

require('dotenv').config()

const options = {
  key: fs.readFileSync(path.resolve(process.cwd(), 'src/api.nodejs.com.key')),
  cert: fs.readFileSync(path.resolve(process.cwd(), 'src/api.nodejs.com.crt')),
  // 보안키(.pem) 생성 중 passphrase를 입력했다면 그 값을 넣어야함.
  passphrase: '10rhrnak',
}

const port = process.env.PORT || 3030
const app = express()

db.connect()

const server = spdy.createServer(options, app)

server.listen(port)
```

### Node.js 보안 모듈들

1. csurf

[csrf 이해하기](https://github.com/pillarjs/understanding-csrf/pull/10/files?short_path=2c41220)

CSRF 혹은 XSRF 공격을 방어하기 위한 수단으로 csrf 토큰을 사용할 수 있다. 이 경우 동작 방식은

```text
1. 서버가 클라이언트로 토큰을 전송합니다. (쿠키의 형태로 전달)
2. 클라이언트가 폼을 토큰과 함께 제출합니다. (react app에서는 credential 옵션을 통해 헤더로 전달)
3. 토큰이 올바르지 않으면 서버에서 요청을 거부합니다.
```

이 때 공격자가 토큰을 얻으려할 것이며, CORS를 허용하지 않음으로써 토큰 획득을 원천적으로 차단할 수 있습니다. 또 쿠키를 httpOnly로 설정하면 XSS(자바스크립트 조작)을 막을 수 있습니다.

2. rate limiting

DDOS(Distributed Denial Of Service): 분산 서비스 거부 공격은 보통 서버 컴퓨터에 막대한 연산을 초래하는 api 요청을 지속적으로 보내거나 혹은 단순히 많은 요청을 보내 서버의 응답 지연이나 응답 시간 초과를 유도하여 서비스를 마비시킨다. 이를 막으려면 어떤 ip에서 어느 정도의 요청을 보내는지를 기억해야하며, 이 경우 redis 등의 저장소를 통해 요청 횟수를 기록하고, 이 숫자를 상회할 경우 에러를 띄울 수 있다.

3. safe-regex

regex 중에는 막대한 연산을 필요로 하는 경우가 있다. 이 경우 DDOS 공격의 표적이 되기 쉬우며, 그렇기 때문에 안전한 regex만을 사용해야 한다. 이 때 "Safe regex"와 같은 모듈을 사용할 수 있다.

[safe-regex](https://github.com/substack/safe-regex)

4. helmet

helmet은 express앱을 위한 보안장치이다. 기본적인 보안조치들이 갖춰져 있으며, 사용법도 간단해서 사용하기 좋다. 필수!

5. user parameter validation

유저가 입력한 값은 언제나 믿을 수 없는 값이다. 특히나 인풋 텍스트 같은 경우 SQL 인젝션이나 위험한 자바스크립트 공격이 언제든 시도될 수 있으므로 입력값을 검증하고 필요하다면 escape 시켜야한다. 또 가능한한 JSON 형태로 정보를 주고 받는 것이 좋다. 이 때 사용할 수 있는 것이 express-validator이다. 입력 검증 미들웨어 중에서는 가장 star가 많다.

[express-validator](https://github.com/ctavan/express-validator)

6. XSS

XSS 방어는 주로 entity를 escape하는 것으로 이뤄진다. 또 다른 방법은 httpOnly cookie를 사용하는 것으로 이를 통해 공격자가 자바스크립트로 cookie를 제어할 수 없게 한다.

### cookie/session 기반 인증

http는 기본적으로 두 가지 특성이 있다. 첫 번째는 상태가 없다는 것이고(stateless), 두 번째는 연결이 일회성이라는 것이다.

이 두 가지 특성 때문에 상태를 저장할 공간이 필요했는데, 일반적으로 현재는 쿠키와 세션이 사용되고 있다.

유저가 성공적으로 로그인하면 그 정보를 담은 세션을 서버에 저장하고 브라우저에는 쿠키를 넘긴다. 성공적으로 로그인된 유저는 이후 요청을 보낼 때는 쿠키와 함께 요청을 보낸다. 만약 쿠키가 올바르다면 세션과 대조를 한 후 서버는 응답한다.

vs. JWT

jwt 방식은 쿠키/세션과 달리 서버쪽에 세션을 생성하지 않는다. 대신 유저의 로그인 정보 등을 토큰 안에 담아 직접 전달한다. 그러므로 쿠키/세션 방식에 비해 서버에 부담이 적다. 그러나 세션을 관리할 수 없으므로 토큰을 임의로 폐기할 방법이 없다.

### nginx 서버 기본적인 proxy, load-balancing과 caching

1. Proxy

proxy 설정은 proxy_pass 옵션을 통해 설정한다.

```conf
server {
  listen 80; # http 기본 포트
  server_name localhost # 서버의 호스트명

  location / {
    # 이 경우 proxy_pass는 WAS(웹앱 서버의 주소를 적어준다.)
    proxy_pass http://127.0.0.1:3001;
  }
}

이 경우 모식도는

유저 -> localhost:80 혹은 localhost로 접속 -> nginx 프록시 서버 -> WAS(node.js 애플리케이션 서버)
```

2. Load-balancing

여러 서버 인스턴스 간에 부하를 조절할 수 있다. 

가장 기본적인 방식은 round-robin(순차적으로 부하를 부담), least-conn(가장 부하가 적은 서버에 부하를 넘김), weight(부하의 정도를 설정)이 있다.

```conf
upstream react {
  least_conn; # least_conn 방식
  server 127.0.0.1:3001 weight=3; # weight 설정
}
server {
  listen 80; # http 기본 포트
  server_name localhost # 서버의 호스트명

  location / {
    # 이 경우 proxy_pass는 WAS(웹앱 서버의 주소를 적어준다.)
    proxy_pass http://react;
  }
}
```

3. Caching

웹앱의 성능에 가장 큰 영향을 미치는 요소는 캐싱이다.

일반적으로 많이 변하지 않는 정적 자산에 대해 캐싱을 시도한다.

캐싱을 위한 설정을 아래와 같다.

```conf
server {
  listen 80; # http 기본 포트
  server_name localhost # 서버의 호스트명

  location / {
    # 이 경우 proxy_pass는 WAS(웹앱 서버의 주소를 적어준다.)
    proxy_pass http://react;
  }

  # ~*은 case-insensitive를 의미, 이런 식으로 regex도 활용가능하다.
  location ~* \.(jpe?g)$ {
    # 애플리케이션의 정적 자산이 들어있는 디렉토리
    root /Users/leejunhyung/ssr/public/img;
    expires 168h; # 만료기간
  }
}
```

### React SSR

서버사이드 렌더링은 크게 두 가지 이점이 있다.

1. SEO 향상
서버측에서 html을 렌더링해서 넘겨주므로 크롤링 봇들이 내용을 바로 확인할 수 있다.

2. 초기 로딩속도 개선
클라이언트측에서 렌더링하는 대신 서버에서 넘겨주므로 초기 로딩속도가 빨라진다.

3. 단점:
서버의 부하가 증가한다.
구조가 복잡해진다.

구현

React 16의 renderToNodeStream을 사용

```js
import express from 'express'
import React from 'react'
import { renderToNodeStream } from 'react-dom/server' // react 16
import App from '../src/App'
import { ServerStyleSheet } from 'styled-components'
import { StaticRouter as Router } from 'react-router-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import reducer from '../src/reducers'
import serialize from 'serialize-javascript' // json 데이터 escape
import path from 'path'

const port = 3001
const server = express()

server.use(express.static(path.resolve(process.cwd(), 'public/img')))

server.get('/', (req, res) => {
  const store = createStore(reducer)
  const preloadedState = store.getState() // 미리 상태를 불러옴

  res.write(
    `<!DOCTYPE html>
      <html>
        <meta charset="UTF-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1.0"
        />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        <title>Document</title>
        <body>
          <img src="/a.jpeg" />
          <div id="root">
          <script>
            window.__PRELOADED_STATE__ = ${serialize(preloadedState)}
          </script>
    `,
  )
  const sheet = new ServerStyleSheet()
  const body = sheet.collectStyles(
    <Provider store={store}>
      <Router context={{}} location={req.url}>
        <App />
      </Router>
    </Provider>,
  )
  const stream = sheet.interleaveWithNodeStream(renderToNodeStream(body))

  stream.pipe(res, { end: false })
  stream.on('end', () => res.end('</div></body></html>')) // html을 닫아줌
})

server.listen(port)

```

## 2018. 03. 24. 토

### html5 모바일 전용 기능들

1. Media Capture

사진 촬영, 비디오 촬영, 녹음 기능이 존재.

## 2018. 03. 25 일

### Typescript / SSR

* Typescript 와 React

주의점: .ts 파일에서는 jsx 문법을 사용할 수 없다. 무조건 .tsx 인지 확인할 것!
장점: 런타임에서 에러를 잡아줌 / IDE 의 자동 완성 기능을 최대치로 사용할 수 있음

* SSR 적용하기

typescript 와 server side rendering 을 적용하며 배운 것들을 기록.

Server side rendering

장점: 서버에서 html 을 미리 렌더링한 후 클라이언트에게 넘겨주므로 초기 로딩 시간이 짧아진다.
그리고 SEO 를 향상시킨다. (미리 결과물을 렌더링해서 넘겨주므로)

단점: 서버의 부하가 늘어난다, 프로젝트가 복잡해질 수 있다, 번들의 규모가 작으면 큰 이점이 없을 수 있다.

commonjs/esnext

적용방법:

* create-react-app 의 typescript 버전을 다운로드 ([링크](https://github.com/wmonk/create-react-app-typescript))

* 그 다음으로 express 서버를 만들어준다.

```jsx
import * as express from 'express'
// 타입스크립트의 type definitions
import { Application, Request, Response } from 'express'
import * as React from 'react'
// React 16의 기능
import { renderToNodeStream } from 'react-dom/server'
import { StaticRouter as Router } from 'react-router-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
// styled-components의 ssr 유틸리티
import { ServerStyleSheet, injectGlobal } from 'styled-components'
import * as path from 'path'

import reducer from 'store/reducers'
import globalStyle from 'styles/global-style'

import App from './App'
import html from './html'

const port = process.env.PORT || 3001
const server: Application = express()

server.use(express.static(path.resolve(process.cwd(), 'public/img')))

server.get('/', (req: Request, res: Response) => {
  const store = createStore(reducer)
  // head의 script tag안에 들어감
  const preloadedState = store.getState()
  const sheet = new ServerStyleSheet()

  /* tslint:disable:no-unused-expression */
  injectGlobal`
    ${globalStyle}
  `

  const body = sheet.collectStyles(
    <Provider store={store}>
      <Router context={{}} location={req.url}>
        <App />
      </Router>
    </Provider>,
  )

  res.write(
    html({
      title: 'GPS NOREABANG FINDER',
      state: preloadedState,
    }),
  )

  const stream = sheet.interleaveWithNodeStream(renderToNodeStream(body))

  stream.pipe(res, { end: false })
  stream.on('end', () => res.end('</div></body></html>'))
})

server.listen(port)
```

* html 파일

```jsx
// JSON.stringify의 대안
import * as serialize from 'serialize-javascript'

export default ({ title, state }: { title: string, state: object }) => `
<!DOCTYPE html>
<html>
  <meta charset="UTF-8" />
  <meta
    name="viewport"
    content="width=device-width, initial-scale=1.0"
  />
  <meta http-equiv="X-UA-Compatible" content="ie=edge" />
  <title>${title}</title>
  <body>
    <div id="root">
    <script>
      window.__PRELOADED_STATE__ = ${serialize(state)}
    </script>
`
```

* index.tsx

```jsx
import * as React from 'react'
import { hydrate } from 'react-dom'
import { consolidateStreamedStyles } from 'styled-components'
import { Provider } from 'react-redux'
import configureStore from 'store/configureStore'

import App from './App'
import registerServiceWorker from './registerServiceWorker'

// as any => 강제 타입 캐스팅
const preloadedState = (window as any).__PRELOADED_STATE__

delete (window as any).__PRELOADED_STATE__

const store = configureStore(preloadedState)

consolidateStreamedStyles()

hydrate(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root') as HTMLElement,
)
registerServiceWorker()
```

* 시작하기(with nodemon)

```bash
NODE_PATH=src nodemon src/server.tsx --exec ts-node --watch src
```

** 먼저 ts-node 를 다운로드 (yarn add --dev ts-node)
** tsconfig.json 을 수정

```json
{
  "compilerOptions": {
    "outDir": "build/dist",
    "module": "commonjs", // 기본 설정은 esnext
    "target": "es5",
    "lib": ["es6", "dom"]
    ...
  }
}
```

### styled-components 유틸리티

* style-lint

styled-components 를 위한 linter ([링크](https://github.com/styled-components/stylelint-processor-styled-components))

* styled-system

styled-components 를 위한 디자인 시스템 ([링크](https://github.com/jxnblk/styled-system))

* vscode-styled-components

styled-components syntax highlighting 지원

* 디자인 시스템

[Carbon Design System](http://carbondesignsystem.com/)

[Pricelinelabs Design System](https://github.com/pricelinelabs/design-system)

### Typescript / React snippets

1.SFC (Stateless Functional Component)

먼저 파일의 확장자가 .tsx 인지 확인해보자 아닐 경우 에러가 남

```tsx
import * as React from 'react'
import { Button } from './shared'

interface Props {
  // string literal type 지원
  name: 'hello' | 'world'
  class?: number
}

const HomeButton: React.SFC<Props> = ({ name }) => <Button>{name}</Button>

export default HomeButton
```

2.Component(class)

```tsx
import * as React from 'react'
import { connect } from 'react-redux'

import * as actions from 'store/test/testActions'

interface ClassProps {
  test: typeof actions.test // 이런 식으로 변수로부터 type 지정 가능
  thunk: typeof actions.thunk
}

// <Props, State> 순임
class Class extends React.Component<ClassProps, any> {
  render() {
    return <div onClick={() => this.props.thunk(22)}>a</div>
  }
}

export default connect(state => state, actions)(Class)
```

3.Redux ActionTypes

```ts
// enum 타입 => object 처럼 사용할 수 있다.
export enum ActionType {
  REQUEST = 'actionType/REQUEST',
}

export const EXAMPLE = 'EXAMPLE'
export type EXAMPLE = typeof EXAMPLE

// RootAction type
export type RootAction = ActionType | EXAMPLE
```

4.RootReducer

```ts
import { combineReducers } from 'redux'

import test, { Test } from 'store/test/testReducer'

// 전체 상태를 export
export interface ApplicationState {
  test: Test
}

export default combineReducers<ApplicationState>({
  test,
})
```

5.Example Reducer

```ts
import { createReducer } from 'store/utils'
import { Record } from 'immutable'

import * as types from '../actionTypes'

const TestRecord = Record({
  number: 0,
  a: 12,
})

export class Test extends TestRecord {
  number: number
  a: number
}

// record를 사용하려면 initialize 해야함
const initialState = new TestRecord()

export default createReducer(initialState, {
  // types.Action => 전체 액션 type
  [types.EXAMPLE](state: Test, action: types.Action) {
    return <Test>state
  },
})
```

6.Example Action

```ts
import * as types from 'store/actionTypes'
import { Dispatch, Action } from 'redux'

import { ApplicationState } from 'store/reducers'

export const test = (id: string) => ({
  type: types.ActionType.REQUEST,
  payload: id,
})

/* tslint:disable:no-console */
export const thunk = (t: number) => {
  return async (
    // 전체 state를 generic의 인수로 받음
    dispatch: Dispatch<ApplicationState>,
    getState: () => ApplicationState,
  ): Promise<Action | undefined> => {
    // promise를 반환
    const x = getState()
    console.log(x.test.a)
    try {
      await setTimeout(() => console.log(t), 100)
      return dispatch({ type: types.EXAMPLE })
    } catch (error) {
      console.log(error)
      return
    }
  }
}
```

### xx.defaults is not a function

이 같은 경우는 import 방식 때문에 발생하는데

해결 방법은 import 대신 require 를 사용하는 것이다.

혹은 es 6 의 전체 모듈 가져오기 syntax 를 사용하면 된다.

```js
import * as modules from 'module'

// or

const modules = require('module')
```

## 2018. 03. 27 화

### cannot find name describe

타입스크립트의 경우 export 된 type 이 없을 경우 이런 에러를 뱉는다.

```bash
yarn add --dev @types/jest # 테스팅 프레임워크
```

### Node.js frameworks(프레임워크)

주목할만한 프로젝트:
fastify: [링크](https://github.com/fastify/fastify)
nest: [링크](https://github.com/nestjs/nest)

fastify 의 장점: 요청을 처리하는 속도가 다른 framework 에 비해서 빠르다.

nest 의 장점:
1.typescript 와 함께 나온 node framework
2.angular 스타일의 코드
3.dependency-injection 가 잘 적용되어 사용하기 "굉장히" 편함 4.테스팅도 지원이 잘 돼있음
5.express.js의 abstraction layer이기 때문에 기존의 라이브러리와의 공존도 가능함

## 2018. 03. 29 목

### flexbox와 margin 속성

부모 element에 flexbox가 적용돼있다면 margin: auto가 설정된 자식 element가 수평 뿐만 아니라 수직 정렬도 된다.
(일반적으로는 수평 정렬만 됨)

### css3 feature query

css3에는 browser에서 적용되는 속성인지 아닌지 확인할 수 있는 방법이 있다.

```css
/* grid가 지원되지 않는 브라우저를 위한 fallback style */
@supports not (display: grid) {
  background-color: red;
}
```

다만 feature query가 지원되는 브라우저의 경우 대부분 최신 기능을 지원하므로, not 보다는 아래와 같이 하는게 좋다.

```css
/*
 * fallback style
 * ie6, firefox 39 등 오래된 브라우저
 */
background-color: red;

/* 최신 브라우저에 적용되는 style */
@supports (display: grid) {
  background-color: red;
}
```

### css3 writing mode

css에는 다국어 지원을 위해 writing mode가 있다. writing mode를 이용해 세로 쓰기나 뒤집은 세로 쓰기 등을 사용할 수 있다.

### css3 grid-auto-flow

grid-auto-flow를 사용하면 알아서 element가 위치를 찾아간다.

## 2018. 03. 31 토

### typescript-create-react-app에서 image(png/jpg 파일) 등 불러오기

typescript는 적절한 typing이 없을 경우 경고 메시지를 뿜는다. 그러므로 프로젝트 아래에 typings 폴더를 만들고(이름은 상관 없다) 그 아래에 다음과 같은 파일을 만들어준다.

```ts
// assets.d.ts
declare module '*.gif'
declare module '*.jpg'
declare module '*.jpeg'
declare module '*.png'
declare module '*.svg'
```

불러올 때는

```ts
import * as jpg from 'path/to/img.jpg'
```

[참조](https://github.com/wmonk/create-react-app-typescript/blob/master/packages/react-scripts/template/README.md#adding-images-fonts-and-files)