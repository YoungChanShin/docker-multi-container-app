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

```bash
$ docker-compose up --build
```

위 명령으로 실행시킨다.



## dockerrun.aws.json

```json
{
    "AWSEBDockerrunversion": 2,
    "containerDefinitions": [
        {
            "name": "frontend",
            "image": "chan8149/docker-frontend",
            "hostname": "frontend",
            "essential": false,
            "memory": 128
        },
        {
            "name": "backend",
            "image": "chan8149/docker-backend",
            "hostname": "backend",
            "essential": false,
            "memory": 128
        },
        {
            "name": "nginx",
            "image": "chan8149/docker-nginx",
            "hostname": "nginx",
            "essential": true,
            "memory": 128,
            "portMappings": [
                {
                    "hostPort": 80,
                    "containerPort": 80
                }
            ],
            "link": [
                "frontend",
                "backend"
            ]
        }
    ]
}
```

`essential`:

`link`



## VPC

virtual private cloud. 

## RDS

비밀번호: qkrwhdwo(박종재)



## security Group(방화벽)



## travis-ci

provider : 외부 서비스 표시- elastic beanstalk

region :지역

bucket_name: 소스코드를 s3에 넣은 다음 배포하는 곳에 넣어주기 때문이다.



## 배포 에러 1

```json
// DockerrunVersion:2 에서 V를 소문자로 써서 최초에러 발생
```

에러 메세지

| 2020-11-19 18:02:51 UTC+0900 | ERROR | Failed to deploy application.                                |
| ---------------------------- | ----- | ------------------------------------------------------------ |
| 2020-11-19 18:02:50 UTC+0900 | ERROR | ECS Application sourcebundle validation error: AWSEBDockerrunVersion is missing. |



## 배포 에러 2

| 2020-11-19 18:11:29 UTC+0900 | ERROR | ECS task stopped due to: Task failed to start. (backend: nginx: frontend: ) |
| ---------------------------- | ----- | ------------------------------------------------------------ |
| 2020-11-19 18:11:28 UTC+0900 | ERROR | Failed to start ECS task: arn:aws:ecs:ap-northeast-2:556121139142:task/awseb-DockerFullstackApp-env-ekqrdnd3sh/7a14833862c5481bb941eff108630257 is STOPPED. |

### trial1

`docker-compose.yml` 파일에서 Dockerf	ile이 dev버전으로 설정되어 있는 것을 `Dockerfile로 바꾸었다. ==> 같은 에러 발생

### trial2

`.travis.yml`파일의 aws_access_key의 indent를 수정했다. ==> 같은 에러 발생

### trial3

frontend의 Dockerfile의 오타를 수정했다. ==> 같은 에러 발생

### trial4

`Dockerrun.aws.json`파일의 오타를 수정했다. `nginx`의 `link`를 `links`로 수정했다.

새로운 에러가 발생했다.

| 2020-11-20 08:36:29 UTC+0900 | ERROR | During an aborted deployment, some instances may have deployed the new application version. To ensure all instances are running the same version, re-deploy the appropriate application version. |
| ---------------------------- | ----- | ------------------------------------------------------------ |
| 2020-11-20 08:36:29 UTC+0900 | ERROR | Failed to deploy application.                                |
| 2020-11-20 08:36:29 UTC+0900 | ERROR | Unsuccessful command execution on instance id(s) 'i-017317da96e79671a'. Aborting the operation. |



### trial5

elasticbeanstalk를 삭제하고 `rds`, `vpc`설정을 다시 해주었다. 그리고 `aws.dockerrunVersion.json`파일도 다시 설정했다.