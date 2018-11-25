---
title: "[Docker] Docker(도커)란?"
excerpt: "Docker 소개와 기본 개념, 예제"
search: true
sidebar_main: true
categories:
  - CloudComputing
tags:
  - Docker
---

# Docker란?

![docker logo](https://user-images.githubusercontent.com/34755287/48662248-bc639d00-eac2-11e8-8443-1d735141d25f.JPG)

Docker는 부두 노동자라는 영어 단어로서, Linux 기반의 Container RunTime 오픈소스 가상화 플랫폼이다.

## 등장 배경
여러 OS와 플랫폼의 등장으로 서버를 관리하는데 비용이 너무나 커졌다. Linux, Windows, Mac과 같이 여러 운영체제가 존재하고 Linux 내에서도 CentOS, Ubuntu 등등 다양하다. 이에 더해 현재에는 클라우드 서비스가 활발하며, 클라우드 역시 AWS, Azure, 구글 클라우드 등 서버를 운영하기 위한 환경이 매우 다양하다.

하나의 서버에서 여러 프로그램을 설치하는 것에는 많은 충돌이 발생한다. 현재에는 위와 같이 여러 환경이 존재하기 때문에 가상머신을 여러 개 사용해야한다. 하지만 가상머신 기술은 전체 운영체제를 설치하는 방식이므로 매우 느리고 관리하기 힘들다. 이를 해결하기 위해 Docker가 등장한다.

### 가상머신(Virtual Machine)
가상머신은 하드웨어를 소프트웨어적으로 구현해서 그 위에서 운영체제를 작동하도록하는 기술이다. 즉, 리얼머신에서 하나의 운영체제가 동작하고 있는 상황에서 가상머신을 통해 다른 운영체제를 하나 더 사용할 수 있다.

가상화의 방식에는 전가상화(Full-Virtualization)과 반가상화(Para-Virtualization)으로 나뉜다.

전가상화는 하드웨어 자원을 완전히 가상화하는 방식이다. 그러므로 Guest OS를 아무런 수정없이 쉽게 도입이 가능하다. 하지만, CPU의 VT(Virtual Technology)를 이용하므로 오버헤드가 상당히 크다. 전가상화를 사용하는 플랫폼은 VMware, VirtualBox 등이 있다.

반가상화는 게스트 OS를 수정하여 게스트 OS가 가상화되고 있음을 인식하도록 하여 하이퍼바이저(Hyperviser, 가상화를 지원하는 소프트웨어)가 필요할 때만 호출되도록 한다. 반가상화는 게스트 OS를 수정해야 하므로 OS 소스코드에 접근가능해야 하므로 도입이 어렵다. 하지만, 가상화를 호출하는 빈도를 최소화하여 성능을 대폭 향상시킬 수 있다. 반가상화는 대표적으로 Xen이 있다.

![virtualization](https://user-images.githubusercontent.com/34755287/48662230-82929680-eac2-11e8-87ed-4883d3912497.JPG)

가상머신 자체는 완전한 게스트 운영체제를 설치해야 하므로 용량이 크고 반가상화라 할지라도 리얼 머신에 비해 성능이 크게 감소된다.

### Docker
Docker는 반가상화보다도 조금 더 경령화된 방식이다. Docker는 게스트 OS 자체를 설치하지 않고 운영에 필요한 프로그램과 라이브러리만 설치하여 이미지 크기를 대폭 줄였다. 그리고 시스템 콜과 같은 OS 자원은 호스트 OS와 공유한다.

![docker](https://user-images.githubusercontent.com/34755287/48662228-82929680-eac2-11e8-9938-cdfdab5daec7.JPG)

Docker는 하이퍼바이저가 없기 때문에 리얼 머신과 성능이 거의 비슷하다. 아래의 표를 보면 확인할 수 있듯이 CPU, 메모리 접근, 파일 시스템 성능이 거의 똑같이 나오는 것을 볼 수 있고, 네트워크 속도 역시 비슷하게 측정된다고 한다.

![docker performance](https://user-images.githubusercontent.com/34755287/48662227-82929680-eac2-11e8-8689-84ba6224f7f0.JPG)

## Docker 구성
Docker는 크게 컨테이너(Container)와 이미지(Image)로 구성되어 있다. 컨테이너는 리눅스의 컨테이너 기술에서 왔으며, 이미지는 컨테이너 실행에 필요한 파일이다.

### 리눅스 컨테이너
LXC(Linux Container)는 운영 시스템 레벨의 가상화 방법이다. 단일 호스트 환경에서 여러 개의 고립된 리눅스 시스템(컨테이너)들을 사용하기 위해 만들어졌다. 이를 위해 리눅스 커널안에서 고립된 가상의 공간을 만든다고 볼 수 있다.

컨테이너는 가상머신과 달리 호스트 OS에서 바로 시작하여 훨씬 성능이 좋다. 컨테이너는 자체적인 그룹화를 시행하여 다른 그룹이나 그룹에 속하지 않는 프로세스들과 단절된 공간을 만든다. 컨테이너간 역시 서로 내부를 볼 수 없다.

LXC는 리눅스 커널의 cgroups(Control Groups)와 namespaces(Namespace Isolation)를 결합하여 고립된 공간을 만든다. cgroups는 CPU, 메모리, 블록 I/O, 네트워크 등과 같은 자원을 할당한다. namespace isolation은 애플리케이션 입장에서 프로세스 트리, 네트워크, 사용자 ID, 마운트된 파일 등을 호스트와 완전히 격리하여 고립된 운영 환경을 만들고, 이는 cgroups에서 제공한다.

![linux container](https://user-images.githubusercontent.com/34755287/48662229-82929680-eac2-11e8-85a4-7a84fb42f490.JPG)

LXC는 리눅스가 제공하는 기술 중 하나인 "chroot"와 비슷하다. chroot는 프로세스의 루트 디렉토리를 변경하는 명령으로, 프로세스가 접근 할 수 있는 디렉트로리를 제한하거나 시스템 라이브러리와 관련 라이브러리를 로드할 수 있다. 하지만 이것만으로는 네트워크와 프로세스를 제어할 수 없었다. 이를 해결하기 위해 더 발전시킨 "jail" 기능이 만들어졌다. LXC는 jail과 유사한 개념으로 여러 자원을 제어 및 격리하는 기능이 구현되어 있다.

Docker는 초기에는 LXC기반으로 컨테이너 생성 및 관리 기능 등 다양한 기능을 추가하였다. 버전 0.9 이후로는 libcontainer를 자체적으로 개발하여 사용하고 있으며, 옵션을 통해 LXC와 libcontainer를 선택적으로 사용할 수 있다.

![docker container](https://user-images.githubusercontent.com/34755287/48662224-81fa0000-eac2-11e8-840f-a28818bd0124.JPG)

### 이미지(Image)
Docker 이미지는 보통 2가지로 구성되어 있다. 첫 번째는 리눅스 운영체제이다. 어떤 프로그램(예를들어, Nginx, MySQL 등)을 실행하려면 운영체제가 있어야 한다. Docker 이미지에서는 기본적으로 리눅스 배포판을 사용한다. 배포판에는 리눅스 부팅에 필요한 최소한의 실행 파일과 라이브러리가 존재한다. 배포판만으로도 패키징 시스템을 사용할 수 있다. 물론 다른 필요한 기능들을 추가하여 이미지를 만들 수도 있다. 두 번째는 사용할 애플리케이션이다.

Docker 이미지 크기는 수메가에서 수기가바이트까지 필요에 따라 다양하지만, 운영체제 하나가 수십기가바이트이므로, 훨씬 작은 크기인 것을 알 수 있다.

Docker 이미지는 Docker hub를 통해 대부분 공개되어 있어 필요에 따라 적절한 이미지를 찾아 사용할 수 있고, 자신이 만든 이미지 역시 등록할 수 있다.

### 컨테이너(Container)
컨테이너는 이미지 파일을 실행한 상태라고 볼 수 있다. 하나의 이미지로 여러 컨테이너를 만들 수 있고, 이들은 모두 독립적이다. 운영체제로 보면 이미지는 실행 파일이고, 컨테이너는 프로세스라고 볼 수 있다. 이미 실행된 컨테이너에서 해당 이미지가 변경된 부분을 바로 새로운 이미지로 만들 수도 있다.

## Docker layer
Docker 이미지를 수정할 때마다 수백메가의 크기를 계속 다시 다운로드받는다면 엄청난 비효율을 가져온다. Docker는 이를 해결하기 위해 레이어(layer)라는 개념을 도입하여 유니온 파일 시스템(Union File System)을 사용한다. 이를 통해 여러 개의 레이어를 하나의 파일 시스템으로 사용할 수 있다.

Docker 이미지는 읽기 전용 상태이다. 여기서 내용이 바뀌면 해당 이미지를 수정하지 않고, 쓰기 이미지를 생성한 뒤 수정된 내용만을 기록한다. 이러한 방식을 Union mount라고 한다.

예를 들어, Ubuntu 이미지가 ```A, B, C```로 구성되어 있다. 여기서 웹서비스를 제공하기 위해 Nginx를 설치 한다. 그러면 기존의 Ubuntu에 Nginx라는 새로운 내용이 추가되어 새로운 이미지 ```(A, B, C), Nginx```가 만들어진다. 마지막으로 애플리케시션 소스를 추가하면 최종으로 ```(A, B, C, Nginx), Source``` 이미지가 생성된다.

![docker layer](https://user-images.githubusercontent.com/34755287/48662226-81fa0000-eac2-11e8-80a3-cd0a064093db.JPG)

위 그림은 예제를 그림으로 표시한 것이다. 컨테이너를 생성할 때는 읽기/쓰기 레이어(R/W layer)를 기본적으로 추가하여 해당 이미지에서 변경된 내용을 저장한다.

이와 같이 이미지들은 수정될 때마다 새로운 이미지를 만들어내며, 기존의 이미지와 새로 만들어진 이미지 사이에 부모-자식 관계가 만들어진다. 위의 예제를 이러한 의존적 관계로 나타내면 아래 그림과 같다.

Docker 이미지를 생성할 때는 바뀐 부분만 생성한 뒤 부모 이미지와 연결한다. 컨테이너를 실행하면 해당 이미지를 불러온 뒤 부모를 계속 참조하는 방식으로 동작한다.

![docker layer tree](https://user-images.githubusercontent.com/34755287/48670714-5e38c780-eb5f-11e8-8153-a1f4b7b760bb.JPG)

Docker 이미지 파일을 Docker hub에 올릴 때는 부모 이미지들을 모두 포함하여 올린다.

## Dockerfile

```js
FROM node:9.4.0-alpine
COPY app.js .
COPY package.json .
RUN npm install &&\
    apk update &&\
    apk upgrade
EXPOSE  8080
CMD node app.js
```

Docker 이미지를 만들기 위해서는 Dockerfile을 빌드해야 한다. 이 파일은 DSL(Domain Specific Language)를 사용한다. 이 파일은 해당 이미지를 생성하기 위해서 필요한 여러 사전 작업을 명시하여 실행시킨다. 여러 의존성 패키지를 설치하거나 포트를 연결해주는 일 등을 한다.

### DSL
- FROM: 어떤 이미지를 기반으로 할지 설정
- MAINTAINER: 이미지 작성자 정보
- RUN: 이미지에서 스크립트나 명령 실행
- CMD: 컨테이너가 시작되었을 때 스크립트나 명령 실행
- ENTRYPOINT: 컨테이너가 시작되었을 때 스크립트나 명령 실행(docker run에서 처리 방식이 다름)
- EXPOSE: 호스트와 연결할 포트 번호 설정
- ENV: 환경 변수 설정
- ADD, COPY: 이미지에 파일 추가
- VOLUME: 데이터를 호스트에 저장하도록 설정
- USER: 명령을 실행할 사용자 계정 설정
- WORKDIR: 명령을 실행할 디렉토리 설정
- ONBUILD: FROM으로 이미지가 사용될 때 실행할 명령 설정

### 이미지 만들기 예제
간단하게 웹(web)에 글을 출력하는 이미지 파일을 만들어보자. 이를 위해 아래의 3개의 파일이 필요하고, 해당 디렉토리안에서 명령어를 사용해야 한다.

1. Dockerfile

```js
FROM node:9.4.0-alpine
COPY app.js .
COPY package.json .
RUN npm install &&\
    apk update &&\
    apk upgrade
EXPOSE  8080
CMD node app.js
```

2. app.js

```js
var express = require('express')
var os = require("os");
var hostname = os.hostname();
var app = express()

app.get('/', function(req, res) {
  res.send('Hello world from ' + hostname + '! Your app is up and running in a cluster!\n')
})
app.listen(8080, function() {
  console.log('Sample app is listening on port 8080.')
})
```

3. package.json

```json
{
  "name": "hello-world-demo",
  "private": false,
  "version": "0.0.1",
  "description": "Basic hello world application for Node.js",
  "dependencies": {
    "express": "3.x"
  }
}
```

- 이미지 빌드(Build)
``` docker image build -t hello-world:1 . ```
  - docker build <옵션> <Dockerfile 경로>
  - ```-t```: tag의 약자로 해당 이미지에 이름을 붙인다.(이름:버전 양식)
- 이미지 확인
``` docker image ls ```
- 이미지 세부 사항
``` docker image inspect hello-world:1 ```
- 이미지 실행
``` docker container run -d -p 8080:8080 -v /root/date:/date --name hello-world-a hello-world:1 ```
  - ```-d```: 컨테이너를 백그라운드로 실행한다.
  - ```-p Host Port:Container Port```: 호스트 포트와 컨테이너 포트를 연결하고 외부에 노출시킨다. http://<Host IP>:8080에 접속하면 컨테이너의 8080번 포트로 접속된다.
  - ```-v Host Directory:Container Directory```: volume의 약자로 호스트의 디렉토리를 컨테이너 디렉토리에 연결한다. 그러면 호스트의 디렉토리에 파일을 컨테이너에서 사용할 수 있다.(변경사항들도 모두 적용된다.)
  - ```--name```: 이미지 이름을 설정한다.
- 컨테이너 확인
``` docker container ls ```

- 실행 결과 화면

![docker image build example](https://user-images.githubusercontent.com/34755287/48662225-81fa0000-eac2-11e8-8f55-1c048df49658.JPG)

## 참고 자료
- [전가상화와 반가상화](https://m.blog.naver.com/PostView.nhn?blogId=brickbot&logNo=220413822823&proxyReferer=https%3A%2F%2Fwww.google.co.kr%2F)
- [리눅스 컨테이너](http://www.opennaru.com/openshift/docker/what-is-the-difference-between-docker-lxd-and-lxc/)
- [Docker](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)
- [Docker 예제](https://github.com/IBM/container-service-getting-started-wt/tree/master/Lab%201)
