# 직접 설정해보는 Mongodb/Redis/Nginx

지난 포스트에서는 최신 버전의 babel 과 webpack 을 통해 node.js 개발환경을 구축하는 것까지 같이 해보았습니다.

이번 포스트에서는 Docker 를 활용해서 Mongodb/Redis/Nginx 를 설정하는 방법을 배워보도록 하겠습니다.

## 0. Why Docker'?'

방학이 한창이던 1 월 말 즈음, 저는 allHumor 프로젝트에 몰두해있었습니다.

이것저것 새로운 기술도 써보고, 코드도 처음부터 리팩토링해보면서 꽤나 큰 고생을 했습니다.

다행스럽게도 개강 직전에 미약하나마 프로젝트를 완성할 수 있었고, 만족스럽지는 않았지만 잘 돌아가긴(?) 했습니다.

근데 막상 AWS 에 배포할 때가 되니 개발환경에서는 예상치도 못했던 문제들이 터져나왔습니다.

제 개발 환경은 맥 + OS Sierra 입니다. 하지만 배포 서버에는 주로 linux 의 여러 배포판들이 사용되죠. (대포적으로 ubuntu, debian, fedora 등) 그

러다보니 라이브러리의 호환성 문제도 터져나왔고(이미지 리사이징을 위해 사용했던 node-sharp 라이브러리가 linux 환경에서 말썽을 일으켰습니다), 서버를 처음부터 세팅할 생각에 막막해졌습니다.

어찌어찌해서 Nginx 와 Mongodb, node.js 등을 세팅하긴 했으나 그 모든 과정을 두 번 하라면 그럴 자신이 없어지더군요.

모든 세팅 명령어를 외워버리거나 어딘가에 적어둘 수도 있지만 버전 업데이트를 해야하거나 새로운 소프트웨어를 설치해야한다면 문제가 이만저만이 아닐 것입니다.

여기서 도커가 등장합니다.

도커에 대한 자세한 설명은 아래 블로그의 훌륭한 글로 대신하겠습니다.

[초보를 위한 도커 안내서 - 도커란 무엇인가?](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)

## 1. Dockerfile 과 Docker-compose

도커를 사용하는 가장 좋은 방법은 Dockerfile 과 docker-compose 설정 파일(.yml)을 사용하는 것입니다.

먼저 node.js app 을 위한 Dockerfile 을 작성해봅시다.

프로젝트의 가장 바깥쪽 디렉토리에 Dockerfile 이라는 이름을 가진 파일을 만들어줍니다. (확장자는 필요없습니다.)

그리고 아래와 같이 작성해줍니다.

```Dockerfile
# 주석은 이렇게 작성합니다.

# alpine 버전은 node.js 공식 이미지보다 몇 배나 가볍습니다.
# 6xx mb vs 13x mb
FROM mhart/alpine-node:9.7.1

# nodemon 설치
# RUN 명령어는 배열로도 사용할 수 있습니다.
RUN yarn global add nodemon

# ADD는 파일을 복사해줍니다.
# 여기서 왼쪽은 호스트 파일의 경로, 오른쪽은 컨테이너의 파일 경로가 됩니다.
# 즉, 현재 프로젝트 디렉토리의 package.json이 컨테이너의 tmp 폴더 아래에 복사됩니다.
COPY ./package.json /tmp/package.json

# 필요한 모듈을 인스톨해줍니다.
RUN cd /tmp && yarn install

# 프로젝트 코드가 위치할 app 폴더를 만들고 node_modules를 복사해줍니다.
RUN mkdir -p /usr/src/app && cp -a /tmp/node_modules /usr/src/app

# WORKDIR은 경로를 설정한 경로로 고정시켜줍니다.
# Dockerfile의 모든 명령어는 기본적으로 /(루트) 디렉토리에서 실행되므로
# 상당히 유용하게 쓸 수 있습니다.
WORKDIR /usr/src/app

# es6를 사용하기 위한 babel 설정 파일
COPY ./.babelrc /usr/src/app

# package.json을 프로젝트 디렉토리로 복사
COPY ./package.json /usr/src/app

# 이제 src 폴더 아래의 코드를 복사해줍니다.
COPY ./src/ /usr/src/app/

# CMD는 명령어를 배열 형태로 배치해야하며
# 실제로 앱을 실행시키는 커맨드가 들어갑니다.
CMD ["yarn", "start"]
```

도커 파일 작성 시 가장 헷갈릴만한 부분은 역시 경로와 관련된 것입니다.

이와 관련해서 기억해두어야할 세 가지는

```text
1. Dockerfile의 각 줄은 경로를 공유하지 않는다.
즉, 명시적으로 WORKDIR 명령어를 통해 새로운 루트 경로를 설정하지 않는 이상 경로는 /에 고정됩니다.

2. WORKDIR을 사용하면 경로를 고정할 수 있다.
만약 같은 경로에서 여러 작업을 해야한다면 WORKDIR을 사용할 수 있습니다.

3. 왼쪽은 호스트 머신의 파일 경로, 오른쪽은 컨테이너의 파일 경로
이는 추후에 volumes을 다룰 때도 똑같이 적용됩니다.
```

Dockerfile 을 작성했으니 build 를 해봅시다.

```bash
docker build . -t server # -t 플래그는 이미지의 이름을 지정해줍니다.
```

이제 도커 컨테이너를 실행시켜봅시다.

```bash
docker run --rm \ # 컨터이너를 한 번 실행시키고 삭제합니다.
--name server \ # 컨테이너의 이름을 지정
-p 3030:3030 \ # 3030 포트로 접근할 수 있게 포워딩
server # 이미지 명
```

참고) 기본적인 도커 명령어

```bash
docker images # 모든 이미지 확인
docker ps # 실행 중인 컨테이너 확인
```

잘 실행되시나요?

이렇게 Dockerfile 을 작성해서 개별로 db/nginx/node.js app 을 실행시킬 수도 있지만 Docker-compose 를 활용하면 한결 간편하게 이 과정을 진행할 수 있습니다.

Docker-compose 설치

[Install Docker Compose](https://docs.docker.com/compose/install/#prerequisites)

설치가 완료되었다면 docker-compose.yml 파일을 작성해줍니다.

Dockerfile 과 같은 디렉토리에 docker-compose.yml 파일을 만들고

```yml
version: "3.5" # 설치된 docker 버전에 따라 다릅니다!
services:
  server:
    container_name: server
    build: . # Dockerfile이 위치한 경로
    ports:
      - "3030:3031" # 연결할 포트
    environment: # 환경변수 설정
      - NODE_PATH=src
      - DB_HOST=mongo
      - DB=test
    networks: # 각 컨테이너를 연결하는 네트워크
      - backend

  mongodb:
    container_name: mongo
    image: mvertes/alpine-mongo # alpine 이미지는 용량이 적어 사용하기 좋다.
    ports:
      - "27017:27017"
    volumes:
      - mongo:/data/db # volume을 만들어두지 않으면 컨테이너가 매번 종료될 때마다 데이터가 초기화 되므로 따로 볼륨으로 관리한다.
    networks:
      - backend # 앱 서버와 같은 네트워크에 연결
      # 만약 이 항목을 넣지 않으면 연결이 없다는 에러가 발생함

networks: # 가장 기본적인 bridge 네트워크
  backend:
    driver: bridge

volumes: # mongodb 데이터
  mongo:
```

package.json 에 아래와 같이 추가해줍니다.

```json
"dependencies": {
  "express": "^4.16.2",
  "mongoose": "^5.0.9"
}
```

모듈을 다운로드 받지 않나구요?

어차피 컨테이너를 빌드하면서 모듈을 컨테이너 다운로드 받기 때문에 호스트 컴퓨터에는 어떤 모듈도 다운로드 받지 않아도 됩니다.

그냥 위와 같이 package.json 에 명시만 해주면 되죠.

이제 src 아래에 db.js 를 추가해줍니다.

```js
import mongoose from 'mongoose'

export default {
  connect: () => {
    // docker-compose 파일에서 environment를 통해 정의된 DB_HOST와 DB를 사용할 수 있습니다.
    mongoose
      .connect(
        `mongodb://${process.env.DB_HOST || 'localhost'}:27017/${
          process.env.DB
        }`,
      )
      .catch(console.log)
  },
}
```

이제 index.js 에서 db 를 연결해줍니다.

```js
import express from 'express'
import db from './db'

const port = process.env.PORT || 3030
const app = express()

db.connect()

app.listen(port, () => console.log(`listening on port ${port}`))
```

이제 두 가지 명령어만 입력하면 됩니다.

```bash
docker-compose build # 새로운 변경사항을 반영
docker-compose up # 서비스 실행
```

잘 실행되시나요? 좋습니다.

혹시 mongodb 커넥션 에러가 발생한다면 environment 에서 변수 값을 잘못 준건 아닌지 체크해주세요! 또 디비 컨테이너가 서버와 같은 네트워크를 사용하고 있는지도 확인해주세요.

## 2. Redis 설정

1.에서 우리는 아주 간단한 Mongodb setup 을 마쳤습니다.

다음 차례는 Redis 입니다.

Redis 설정은 Mongodb 설정과 상당히 유사합니다.

```yml
redis:
  container_name: redis
  image: redis:4.0.8-alpine # 역시 alpine 이미지를 사용해줍니다.
  ports:
    - "6379:6379"
  volumes:
    - redis:/data/redis
  restart: always
  networks: # 서버와 같은 네트워크, 잊지 마세요!
    - backend
```

Redis client 연결을 위한 파일을 만들어주고,

```js
// redis.js
import redis from 'redis'

const client = redis.createClient(
  process.env.REDIS_PORT || 6379,
  process.env.REDIS_HOST || 'localhost',
)

client.on('error', err => {
  console.log(err)
})

export default client
```

아까와 마찬가지로 redis 를 dependency 에 추가해줍니다.

```json
"dependencies": {
  "express": "^4.16.2",
  "mongoose": "^5.0.9",
  "redis": "^2.8.0"
}
```

```bash
docker-compose build # 새로운 변경사항을 반영
docker-compose up # 서비스 실행
```

항상 build 커맨드를 실행시켜주시는거 잊지 말아주세요.

이렇게 간단한 설정 몇 줄과 명령어 몇 번만으로 디비 세팅이 완료되었습니다.

Docker 와 Docker-compose 의 가장 강력한 힘은 설정이 간단하고 곧바로 프로덕션 환경으로 넘어갈 수 있다는 점에 있습니다.

개발단계에서 이렇게 테스트를 바로 진행해볼 수 있기 때문에 개발-프로덕션 환경 간에 차이가 줄어들게 되죠.

계속해서 Nginx 설정으로 마무리 짓겠습니다.

## 3. Nginx 설정

아래와 같이 docker-compose.yml 파일에 추가해준 다음,

```yml
  nginx:
    image: nginx:alpine
    container_name: nginx
    ports:
      - "3031:80"
    networks:
      - backend
```

nginx.conf 파일을 준비해줍니다.

```conf
upstream app {
  server server:3030; # 서버의 컨테이너 명
}

server {
  listen 80;

  location / {
    proxy_pass http://app;
  }
}
```

이제 nginx 를 위한 Dockerfile 을 작성해봅시다.

```Dockerfile
FROM nginx:alpine

# 기본 설정 파일을 지우고, 새로운 파일로 대체합니다.
RUN rm /etc/nginx/conf.d/default.conf
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
```

```bash
docker-compose build # 새로운 변경사항을 반영
docker-compose up # 서비스 실행
```

어떤가요? nginx 로그가 보이시나요?

이로써 정말 간단한 서버 설정이 완료됐습니다.

다음에는 Docker + ELK 스택(엘라스틱서치 + 로그스태시 + 키바나) 설정편으로 돌아오겠습니다.

감사합니다.

## 참고: 같이 읽으면 좋은 글들

[초보를 위한 도커 안내서 - 도커란 무엇인가?](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)
[Building Efficient Dockerfiles - Node.js](http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/)
[자주쓰는 Dockerfile instruction 들](https://rampart81.github.io/post/dockerfile_instructions/)
