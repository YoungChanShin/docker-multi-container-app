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

## nginx

하나의 `location`에 같은 포트로 요청을 받아서 요청 url에 따라 응답을 백 또는 프런트로 주기 위해 필요

## docker-compose.yml

