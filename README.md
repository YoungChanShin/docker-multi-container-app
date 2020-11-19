# Multi-container를 실행하는 배포

| api        | method | 요청 | 응답                              | 역할                              |
| ---------- | ------ | ---- | --------------------------------- | --------------------------------- |
| /api/hi    | get    | {}   | {result:"sucuess"}                | 연결이 잘 되었는지 확인           |
| /api/value | get    | {}   | DB lists테이블에 있는 모든 데이터 | DB lists테이블에 있는 모든 데이터 |
| /api/value | post   | {}   | {success:true}                    | 데이터 삽입 요청                  |

## front-end

- react로 구성

### nginx

프로젝트 전체가 빌드되어 html파일로 정리된 정적파일을 제공하기 위해 필요

## back-end

- node

## mysql

```yaml
  mysql:
    build: ./mysql
    restart: unless-stopped
    container_name: app_mysql
    ports:
      - '3306:3306'
    volumes:
      - ./mysql/mysql_data:/var/lib/mysql
      - ./mysql/sqls/:/docker-entrypoint-initdb.d/
    environment:
      MYSQL_ROOT_PASSWORD: mysql
      MYSQL_DATABASE: myapp
```

`create table`이 되지 않았다. AUTO_CREMENT명령에서 에러가 발생했다고 한다. 결국 workbench를 이용해 쿼리문을 만들었다.

mysql의 데이터는 호스트 파일 시스템의 `/mysql/mysql_data`에 저장되도록 하고 컨테이너도 그 호스트 파일을 참고하도록 설정되어 있어서 컨테이너나 이미지를 삭제하더라도 데이터는 계속 남아 있다. DB를 완전히 삭제하고 싶다면 `/mysql/mysql_data`디렉토리를 삭제하면 된다.

## nginx

하나의 `location`에 같은 포트로 요청을 받아서 요청 url에 따라 응답을 백 또는 프런트로 주기 위해 필요

## docker-compose.yml

