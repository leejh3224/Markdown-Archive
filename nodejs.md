# 로컬에서 node.js 기반 웹앱 구동 환경 설정하기

1. node js LTS 버전 설치 및 git 설치

2. git clone 명령어를 이용, 프로젝트 클론하기

3. 현재 디렉토리를 확인하고, allhumor 디렉토리에서
   cd client 하고, npm install 명령어를 통해 필요한 디펜던시(모듈)을 설치한 다음, npm start 를 통해 클라이언트 서버 구동(React.js 개발서버)

4. cd .. 를 통해 client 디렉토리에서 벗어나고, cd server 를 통해 server 디렉토리에 들어와 아까와 같이 npm install 명령어를 통해 필요한 디펜던시를 설치하고 npm start 를 통해 node js 서버 구동

5. 현재 상태

React.js 개발서버(localhost:3000)

node.js 서버(localhost:3030)

이 중 React.js 서버는 node.js 서버에 프록시 요청(요청하는 호스트 및 포트를 변경)을 통해 node.js 서버로 부터 필요한 자원을 공급받는다.

6. 참고: npm 은 node package manager 의 약자로, node js 를 다운로드 받으면 자동으로 다운로드 된다. node js 의 버전 확인은 터미널에 node --version 명령어를 통해 확인할 수 있다. 만약 버전이 8.x.x 이상이면 ok.

7. node js 의 크롤링을 테스트해보려면 postman 이라는 크롬 확장 프로그램이 필요함.

크롬 앱스토어에서 postman 을 다운로드 받고, 요청방식은 GET, 그 옆에 url 에는 localhost:3030/api/v1.0/crawlers/dogdrip/game/1 이런 식으로 적어준다. 참고로 dogdrip 뒤의 game 은 dogdrip(개드립 메인 게시판), game(게임 게시판), cook(요리 게시판), pic(짤방 게시판) 중 하나로 대체될 수 있다. 그리고 뒤의 숫자는 게시판의 넘버를 의미함.

8. url 과 요청방식을 모두 세팅했다면 send 를 눌러보자.

9. 에러가 뜰 것이다.

10. 현재 컴퓨터에는 몽고디비가 설치되어 있지 않기 때문이다.

11. 몽고디비 설치를 검색하면 os 에 맞는 몽고디비를 설치할 수 있다.

12. 설치가 완료되었다면 C:/data/db 라는 경로를 생성해주자.
    즉, c 드라이브에 data 라는 폴더를 생성 후 바로 data 폴더 아래에 db 폴더를 만들어주자. 이 경로로 몽고디비의 데이터가 저장된다.

13. 이제 터미널을 켜고 루트 디렉토리에서 mongod --dbpath data/db 명령어를 실행시키자. 에러가 뜨지 않았다면 스크립트가 종료되지 않을 것이다.

14. 이제 터미널 창을 하나 더 켜서 mongo 라고 쳐보자. 뭐라고 말이 주욱 나온 뒤에 > 라는 표시가 맨 아래에 뜰 것이다.

15. 이제 명령어를 입력할 수 있다.

16. node js 서버가 알아서 collection(SQL 의 table 개념)을 생성해주므로 딱히 mongodb 에 해줄건 없지만 몇 가지 명령어를 통해 자료를 조회할 수 있다.

* use allhumor(디비 네임) // 디비 사용
* db.articles(collection 명).find() // article collection 의 모든 자료 조회
* db.articles.find({ author: "xx" }) // article collection 의 author 가 xx 인 document 를 검색

17. 몽고디비 설치 및 셋팅이 완료되었다면 이제 postman 을 통해 요청을 시켜보자.

18. 이미지 파일이 저장되고 디비에는 크롤링된 데이터가 보관될 것이다.

19. 이미지 파일의 저장경로는 client/public/article/images/dogdrip 이다. (참고용)
