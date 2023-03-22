## dockerfile

DockerFile 은 코드의 형태로 인프라를 구성하는 방법을 텍스트 형식으로 저장해 놓은 파일이며 docker build 를 사용하여 자신만의 이미지를 만들 때 사용합니다.

예시

```docker
### Base Image 지정
FROM ubuntu:20.04

### TimeZone 환경 변수 지정
ENV TZ Asia/Seoul

### TimeZone 설정    
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

### /home/dev 폴더 생성
RUN mkdir /home/dev

### update 및 패키지 설치     
RUN apt update && apt -y install vim git tar gzip build-essential curl alien openjdk-8-jdk nginx

### nodejs 설
RUN curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_14_setup.sh && bash nodesource_14_setup.sh && apt -y install nodejs

### build, tomcat, maven, nginx(default.conf) 파일 복사   
COPY build.tar.gz /home/dev/build.tar.gz
COPY tomcat-9.0.45.tar.gz /home/dev/tomcat-9.0.45.tar.gz
COPY apache-maven-3.8.1.tar.gz /home/dev/apache-maven-3.8.1.tar.gz
COPY default.conf /etc/nginx/conf.d/default.conf

### ubuntu01 계정 생성
RUN addgroup --gid 1100 ubuntu01 && adduser --disabled-password --home /home/dev --no-create-home --system -q --uid 1000 --ingroup ubuntu01 ubuntu01

### Github Source 파일 다운로드     
RUN git clone https://github.com/bc-hwang/TEST.git /home/dev/deverse

### /home/dev 폴더 이동   
WORKDIR /home/dev

### 암축 파일 해제    
RUN tar -zxvf apache-maven-3.8.1.tar.gz
RUN tar -zxvf tomcat-9.0.45.tar.gz
RUN tar -zxvf build.tar.gz

### maven link 설정
RUN ln -s /home/dev/apache-maven-3.8.1/bin/mvn /usr/bin/mvn

### Build 실행
RUN cd /home/dev/build && bash ./back_build.sh
RUN cd /home/dev/build && bash ./front_build.sh

### Nginx & Tomcat Service 실행    
CMD nginx -g 'daemon on;' && /home/dev/tomcat-9.0.45/bin/catalina.sh run

### 서비스 포
EXPOSE 80 8080
```

## docker ****Multi-stage build****

컨테이너 이미지를 만들면서 빌드 등에는 필요하지만, 최종 컨테이너 이미지에는 필요 없는 환경을 제거할 수 있도록 단계를 나누어 기반 이미지를 만드는 방법




멀티스테이지 빌드를 사용하게 되면 위의 그림처럼 컨테이너 실행 시에는 빌드에 사용한 파일 및 디렉토리과 같은 의존 파일들이 모두 삭제된 상태로 컨테이너가 실행되게 됩니다. 결론적으로 좀 더 가벼운 크기의 컨테이너를 사용할 수 있게 됩니다.

예시

```docker
# 1. Build Image
FROM golang:1.13 AS builder
 
# Install dependencies
WORKDIR /go/src/github.com/asashiho/dockertext-greet
RUN go get -d -v github.com/urfave/cli
 
# Build modules
COPY main.go .
RUN GOOS=linux go build -a -o greet .
 
# ------------------------------
# 2. Production Image
FROM busybox
WORKDIR /opt/greet/bin
 
# Deploy modules
COPY --from=builder /go/src/github.com/asashiho/dockertext-greet/ .
ENTRYPOINT ["./greet"]
```

**1. 개발 환경용 Docker 이미지**

Go의 버전 1.13을 베이스 이미지로 하고 'builder'라는 이름을 붙입니다. 그리고 개발에 필요한 것들을 모두 설치하여 로컬환경의 소스코드를 컨테이너로 복사합니다. 이 후 이 소스코드를 **go build를 통하여 'greet'이라는 실행 가능한 바이너리 파일을 생성합니다.**

**2. 제품 환경용 Docker 이미지**

제품 환경용 Docker 이미지의 베이스 이미지는 'busybox'를 사용합니다.

`COPY --from=builder /go/src/github.com/asashiho/dockertext-greet/ .`

이후 개발용 환경의 **Docker 이미지로 빌드한 'greet'이라는 이름의 바이너리 파일(실행파일)을 제품 환경의 Docker 이미지로 복사**합니다. 이 때 **--from** 옵션을 사용하여 **'builder'**라는 이름의 이미지로 부터 복사한다는 것을 선언합니다. 그리고 복사한 실행 파일을 실행하는 명령어를 적습니다.

이렇게 두 개의 Docker 이미지를 생성할 수 있는 Dockerfile을 작성하였습니다.
