# 쿠버네티스로 컨테이너 서비스 운용하기 (ft_services)

## 컨테이너와 컨테이너 오케스트레이션

옛날에는 인터넷 서버를 운용할 때 서버 (컴퓨터)를 사서 직접 구동시키는 경우 혹은 호스팅 업체로부터 서버를 구매하여 원격으로 접속해 구동시킬 어플리케이션을 서버로 올려서 구동시키는 방법을 사용하였다. 이런 방법들은 서버를 구매하는데 전기세 유지비 등이 많이 들어가는 단점들이 있었다. 또 호스팅 업체가 구동하는 서버 운영체제에 따라 구동이 되거나 안되거나 하는 문제도 있다. 따라서 최근에는 제공하고자 하는 웹 서비스 컨테이너화 시켜 클라우드 컴퓨팅 서비스 (AWS, Azure, Google Cloud 등이 있다.)에 올려서 서비스를 제공한다.

도커로 서비스를 운용한다 생각해 보자. 베이스로 사용할 이미지 (우분투/데비안 등등)를 내려받고 이미지에 여러 프로그램 설치 및 환경 등을 설정하여 새로 이미지를 만든다. 이를 실행하면 컨테이너가 된다.

예를 들어 데비안 기반으로 nginx + PHP + MySQL 기반으로 웹 서버를 도커로 만든다 가정하면 데비안 이미지를 내려받은 후 apt 혹은 수동설치 등 여러 방법으로 nginx, PHP, MySQL을 설치하고 실행할 어플리케이션 (프로그램)을 올려 이미지로 만든 다음에 실행 (컨테이너화)시켜서 사용한다. (이런 작업은 수동으로 하거나 dockerfile을 이용해 자동화 한다.)

하지만 nginx, PHP, MySQL을 각각 다른 컨테이너로 분할해서 혹은 동일한 기능을 하지만 컨테이너를 분할해서 사용할 필요가 있을 것이다. 대표적인 이유는 사용자가 많은데 컨테이너 하나에만 서비스를 구동시키면 서비스가 느려질 것이다. 사용자가 10만명정도 되는데 하나의 서버로 접속자가 몰리면 당연히 속도가 느려질 수밖에 없다.

도커를 이용해 웹 서비스를 제공할 때 컨테이너들을 제어하는 것이 중요하다. 또 웹으로 제공하는 서비스가 많을 수록 컨테이너는 수백개 혹은 수천개 또는 그 이상일 수도 있다. 이런 상황에서 특정 컨테이너가 갑자기 사용 불능이 되거나 하는 상황에서 사람이 일일히 대응해서 작업하기엔 힘들 것이다.

따라서 컨테이너를 기능별 혹은 사용량 별 등 여러 기준으로 분할하고 이런 서버들을 관리해야 할 필요가 있다. 이런 것들을 해 주는 것을 **컨테이너 오케스트레이션 (Container Orchestration)** 이라고 한다.

컨테이너 오케스트레이션이 수행할 수 있는 일들은 아주 다양하다. 보통 컨테이너와 관련된 작업들을 대부분 자동화해서 운영해 줄 수 있도록 해준다.

컨테이너 오케스트레이션 중 가장 유명한 것이 **쿠버네티스 (Kubernetes)** 이다. 가낭 널리 사용하고 오픈소스이다.

## 쿠버네티스

쿠버네티스 외에도 다른것들 (아마존 AWS의 ECS 컨테이너가 오케스트레이션 역할을 하는 기능이라고 한다.)도 있었지만 쿠버네티스가 강력한 기능을 제공해 왔고 현 상태가 유지되어 여러 기업에서 쿠버네티스를 기반으로 해당 기능을 제공한다. 따라서 도커를 이용해 제대로 웹 서비스를 개발하고 배포한다면 쿠버네티스를 알아야 한다.

쿠버네티스에 여러 부분으로 참여하는 기업/단체 혹은 쿠버네티스 기반으로 여러 서비스를 제공한 기업들 또 쿠버네티스 기반으로 여러 서비스를 구동하는 배경 (웹 서버 운용을 넘어선 다른 기능들. 스프링 프레임워크로 웹 서버 외에 다른 기능 개발을 할 수 있는 것과 유사할까?) 속에서 쿠버네티스는 엄청나게 많은 기능이 존재하며 그만큼 개념도 복잡한 편이다.

### 쿠버네티스가 할 수 있는 것

- 로드 밸런싱 (Load Balancing / 부하 분산)

  로드 밸런싱은 위에서 말한 접속자가 많을 때 혹은 적을 때 이에 따른 제어를 해 주는 것을 말한다. 로드 밸런싱은 쿠버네티스만의 기술은 아니다. 하지만 인터넷 서비스 특성상 로드 밸런싱 개념을 많이 사용하고 부하(트래픽)에 따라 쿠버네티스가 원활하게 자원을 제어해 준다.

- 자동 복구 (Self-healing)

  특정 컨테이너가 오류로 종료되거나 하는 상황에서 알아서 해당 컨테이너를 복구해준다. 이런 과정들을 클라이언트가 모르는 새 수행하게 할 수 있다.

- 현재 사용환경에 따른 최적의 자원 분배 (bin packing)

  컨테이너에 따라 필요로 하는 자원 사용량 등이 다를 것이다. 이를 컨테이너가 필요로 하는 만큼 적절히 할당하게 할 수 있다.

- 롤아웃/롤백

  현재 배포중인 서비스를 이전으로 되돌리거나 신규 서비스를 배포하는 것을 말한다. 쿠버네티스로 이를 자동화 할 수 있다.

- 기타 여러가지 기능들

대충 요약하자면 도커 컨테이너를 이용해 서버를 운용할 때 컨테이너 관련해서 실무에서 문제될 만한 혹은 복잡한 일들을 알아서 효율적으로 처리해 준다고 보면 된다.

비유하자면 컨테이너와 쿠버네티스의 관계를 컴퓨터에서 돌아가는 프로그램들과 운영체제의 관계와 유사하다고 할 수도 있다. 운영체제는 실행되는 프로그램을 알아서 자원 할당해서 잘 실행해 주고 쿠버네티스는 실행되는 컨테이너를 컨테이너에 필요한 자원들을 알아서 할당해서 잘 실행되게 만들어 준다.

## 쿠버네티스 구성 요소

### 파드 (Pod)

쿠버네티스에서 컨테이너를 구동시킬 수 있는 가장 작은 단위이다. 하나 이상의 컨테이너들로 구성되어 있으며 파드 내 여러개의 컨테이너는 같은 네트워크를 공유한다. (외부와 차단되어 있다.)

#### 레플리카 셋 (Replica set)

같은 파드가 여러개로 복제된 것들을 **레플리카 셋 (replica set)** 이라고 한다. 직역하면 **복제본 세트** 정도의 의미를 가지므로 단어에서 뜻을 유추할 수 있다. 레플리카 셋의 주 용도는 같은 파드를 여러개 생성해 파드 중 하나가 사용 불능이 되었을 때 쿠버네티스 내부적으로 해당 파드를 종료하고 다른 정상적으로 실핼되고 있는 파드를 이용해 서비스를 제공하며 새로 파드를 생성한다. 이런 과정이 내부적으로 자동으로 이루어진다.

### 노드 (Node)

노드 내에서 파드가 실행된다. 노드는 하나 이상의 파드를 가지고 있다. 따라서 실제 어플리케이션(wordpress라던지 mysql이라던지 컨테이너)은 노드에서 구동된다. kubelet은 노드 내에서 파드 내 컨테이너의 동작을 제어한다. 이 노드는 물리적인 PC가 될 수도 있고 가상머신일 수도 있다.

노드는 최소 3개 이상이 있어야 한다.

### 마스터 (Master)

노드들을 총괄하여 제어하는 역할을 한다. 실질적인 관리는 모두 마스터가 한다고 할 수 있다. 사용자도 노드에 직접 제어를 하는 것이 아닌 마스터에게 노드 제어를 요청하는 식이다. 노드는 마스터와 쿠버네티스 API 라는 것을 이용해 마스터와 통신한다.

#### Deployment (디플로이먼트)

마스터 노드 내에서 파드/레플리카셋의 상태를 제어한다.

### 쿠버네티스 클러스터

최소 3개의 노드와 마스터가 있는 시스템인 쿠버네티스가 실행 중인 서버를 쿠버네티스 클러스터라고 한다. 쿠버네티스를 운용 중이면 해당 서버(들)을 쿠버네티스 클러스터라고 부를 수 있다. 하지만 하나의 독립된 컴퓨터에서만 쿠버네티스 클러스터가 실행될 수 있는 것은 아니다. 여러개의 컴퓨터(서버) 혹은 가상머신들이 묶여서도 클러스터가 될 수 있다.

## 아주 기본적인 클러스터 생성해보기

### minikube

minikube는 가벼운 쿠버네티스 구현체이다. 쿠버네티스 구현체에는 여러 가지 종류가 있는데 학습 혹은 소규모 구축에서는 minikube를 사용한다.

### minikube 설치 (macOS)

```shell
# 방법 1. brew 이용
$> brew install minikube
# 방법 2. minikube 프로그램 내려받아서 실행
$> curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-darwin-amd64
$> chmod +x minikube
```

### minikube 실행

minikibe를 실행한다는 것은 곧 쿠버네티스 클러스터를 생성한다는 말이다.

해당 명령어를 실행하면 minikube가 알아서 쿠버네티스를 내려받고 설치하고 실행시킨다. (아래 과정중 k8s는 쿠버네티스의 다른 명칭이다.)

minikube는 기본적으로 설치하는 환경 내에 가상환경으로 설치가 된다. 아무런 옵션도 넣지 않을 때에는 현재 사용하는 환경에서는 도커 위에 컨테이너를 구동하는 형태로 사용한다. (다른 형태로 버추얼박스 위에서 구동시키거나 로컬에 직접 설치할 수도 있다.)

```shell
$> minikube start
😄  Darwin 11.1 위의 minikube v1.17.0
✨  자동적으로 docker 드라이버가 선택되었습니다. 다른 드라이버 목록: hyperkit, ssh
👍  minikube 클러스터의 minikube 컨트롤 플레인 노드를 시작하는 중
💾  Downloading Kubernetes v1.20.2 preload ...
    > preloaded-images-k8s-v8-v1....: 491.22 MiB / 491.22 MiB  100.00% 35.35 Mi
🔥  Creating docker container (CPUs=2, Memory=1988MB) ...
🐳  쿠버네티스 v1.20.2 을 Docker 20.10.2 런타임으로 설치하는 중
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

현재 환경에서는 쿠버네티스가 도커 위에서 구동되고 있는 것으로 나온다. 따라서 현재 도커에서 운용되고 있는 컨테이너를 보면 minikube라는 이름으로 쿠버네티스가 구동되고 있다.

```shell
$> docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED          STATUS          PORTS                                                                                                      NAMES
e611616fe069   gcr.io/k8s-minikube/kicbase:v0.0.17   "/usr/local/bin/entr…"   15 minutes ago   Up 15 minutes   127.0.0.1:55007->22/tcp, 127.0.0.1:55006->2376/tcp, 127.0.0.1:55005->5000/tcp, 127.0.0.1:55004->8443/tcp   minikube
```

### 파드 생성 및 디플로이먼트 만들기

도커 이미지를 인수로 입력해 이 이미지를 기반으로 한 파드를 만든다. 디플로이먼트는 도커의 마스터 내에 있는 컨트롤러이다. 디플로이먼트가 파드 상태를 모니터링해 파드를 관리하게 된다.

인수로 입력되는 image 경로는 로컬 혹은 원격에 있는 도커 이미지이다. 해당 이미지로 파드를 생성한다는 의미이다.

예제로 들어간 이미지는 nginx에서 배포하는 nginx 공식 이미지이다. (추후에 서비스를 운용한다면 운용하고자 하는 이미지를 넣을 것이다.)

```shell
$> kubectl create deployment newnode --image=docker.io/library/nginx:alpine
deployment.apps/newnode created
```

현재 운용중인 디플로이먼트는 다음과 같이 확인할 수 있다.

```shell
$> kubectl get deployments
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
newnode   1/1     1            1           27s
```

현재 생성된 파드는 다음과 같이 확인할 수 있다.

```shell
$> kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
newnode-649dfbf6dc-fpqvh   1/1     Running   0          37s
```

기타 kubectl 명령어는 [https://kubernetes.io/ko/docs/reference/kubectl/overview/](https://kubernetes.io/ko/docs/reference/kubectl/overview/) 에서 확인할 수 있다.

### 서비스 생성

파드 내에서 동작되는 컨테이너는 클러스터 외부에서 접속할 수 없다. 예를 들면 도커에서 컨테이너를 실행할 때 -p 옵션으로 클러스터와 현재 로컬호스트 사이에 통신할 수 있는 포트를 생성해 연결할 수 있다. 하지만 파드 내에서 동작되는 컨테이너는 이런 방법으로 연결할 수 없다.

쿠버네티스에서는 서비스를 이용해 클러스터 외부로 포트를 노출시킨다.

```shell
$> kubectl expose deployment newnode --type=LoadBalancer --port=80
service/newnode exposed
```

생성한 서비스를 확인해 보자.

```shell
$> kubectl get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        167m
newnode      LoadBalancer   10.104.145.214   <pending>     80:30577/TCP   15s
```

생성한 서비스로 접속해 보자.

```shell
$> minikube service newnode
|-----------|---------|-------------|---------------------------|
| NAMESPACE |  NAME   | TARGET PORT |            URL            |
|-----------|---------|-------------|---------------------------|
| default   | newnode |          80 | http://192.168.49.2:30577 |
|-----------|---------|-------------|---------------------------|
🏃  newnode 서비스의 터널을 시작하는 중
|-----------|---------|-------------|------------------------|
| NAMESPACE |  NAME   | TARGET PORT |          URL           |
|-----------|---------|-------------|------------------------|
| default   | newnode |             | http://127.0.0.1:64353 |
|-----------|---------|-------------|------------------------|
🎉  Opening service default/newnode in default browser...
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
```

해당 명령어를 실행하면 http://127.0.0.1:64353 이라는 경로로 페이지가 열린다.

종료는 다음과 같은 순서대로 한다.

```shell
$> kubectl delete service newnode
service "newnode" deleted
$> kubectl delete deployment newnode
deployment.apps "newnode" deleted
$> minikube stop
✋  Stopping node "minikube"  ...
🛑  Powering off "minikube" via SSH ...
🛑  1개의 노드가 중지되었습니다.
$> minikube stop
🔥  docker 의 "minikube" 를 삭제하는 중 ...
🔥  Deleting container "minikube" ...
🔥  /Users/joohongpark/.minikube/machines/minikube 제거 중...
💀  "minikube" 클러스터 관련 정보가 모두 삭제되었습니다
```

## ft_service 요구사항 분석

### 목표

- 쿠버네티스를 이용해 클러스터를 관리하고 서비스를 배포함.
- 네트워크를 가상화하고 클러스터링 할 수 있어야 함.
- 각각 다른 서비스 (웹서버/데이터베이스 등)로 나눌수 있어야 함.

### 요구사항

- setup.sh 파일을 제외한 모든 설정파일들은 srcs 폴더 내에 있어야 하며 클러스터를 세팅할 수 있는 쉘 파일은 setup.sh 파일로 존재해야 한다.
- 클러스터 구성에 사용할 모든 이미지는 Dockerfile로 빌드가 되어야 한다.
  - 도커 빌드에 사용되는 이미지는 알파인 리눅스 (https://hub.docker.com/_/alpine) 를 사용해야 한다.
- 쿠버네티스 관련
  - 쿠버네티스 웹 대쉬보드에 접속하여 클러스터를 관리할 수 있어야 한다.
  - Load Balancer를 이용해 외부에서 클러스터로 접속할 수 있어야 한다. (다른 경로로 클러스터에 진입할 수 있으면 안된다.)
    - 로드 밸런서는 싱글 아이피를 사용한다.
  - 워드프레스 관련
    - 워드프레스 웹 사이트는 포트 5050에서 동작되어야 한다.
    - 워드프레스와 MySQL 기반 데이터베이스는 서로 다른 컨테이너에서 동작되어야 한다.
    - 워드프레스는 관리자를 포함한 몇 유저들이 생성되어 있어야 한다.
    - 워드프레스는 워드프레스 구동을 위한 nginx 서버가 필요할 것이다.
    - Load Balancer는 워드프레스로 리다이렉트 할 수 있어야 한다.
  - phpmyadmin 관련
    - phpmyadmin은 포트 5050에서 동작되어야 한다.
    -  phpmyadmin 구동을 위한 nginx 서버가 필요할 것이다.
    - Load Balancer는 phpmyadmin으로 리다이렉트 할 수 있어야 한다.
  - nginx 관련 (위 phpmyadmin/워드프레스 nginx와 다른듯)
    - nginx 서버는 80, 440 포트에서 동작되어야 한다.
    - 80 포트는 HTTP 301을 이용해 443으로 리다이렉션 해야 한다.
    - nginx 서버는 아무거나 표시 가능하다. (에러만 아니면 됨)
    - /wordpress로 접근할 시 HTTP 307을 이용해 워드프레스 아이피:포트로 접속할수 있도록 함.
    - /phpmyadmin으로 접근할 시 리버스 프록시를 이용해 워드프레스 아이피:포트로 접속할수 있도록 함.
  - FTPS(파일 전송 프로토콜)는 21번 포트로 동작하도록 함.
  - Grafana 관련
    - 포트 3000에서 동작하도록 함.
    - InfluxDB에 링크되어 있어야 함.
    - 이게 모든 컨테이너를 모니터링 할 것임.
    - 각 서비스에 대한 대쉬보드를 만들어야 함.
    - InfluxDB와 Grafana는 서로 다른 컨테이너 내에 있어야 함.
  - 기타
    - 두개의 데이터베이스 중 하나가 정지된다면 데이터가 유지가 되어야 함.
    - nginx 컨테이너에 SSH로 로그인 할 수 있어야 함.
    - 컴포넌트 중 하나가 정지된다면 컨테이너는 재시작되어야 한다.

## 요구사항에서 말하는 것들

### FTP

#### FTP란?

FTP는 File Transfer Protocol의 약자이자 TCP 기반으로 컴퓨터끼리 파일을 교환하기 위한 역사가 오래된 프로토콜이다.

FTP는 역사가 오래되었기 때문에 보안 관련을 보완한 FTPS/SFTP가 있다.

기본적으로 FTP는 비밀번호를 평문으로 송수신하지만 해당 과제에서는 FTPS를 사용하여 최소한 비밀번호는 암호화 할 수 있도록 해준다.

역사도 오래되었고 포트를 여러개 사용하는 단점도 있지만 서버 간 파일 송수신으로 여전히 많이 사용된다.

FTP(S)의 연결은 Active/Passive 두 가지 모드가 존재하며 둘 다 포트 최소 두개 이상을 사용한다. 보통 FTP 서버가 21번 위에서 동작되지만 데이터 전송은 다른 포트로 진행한다.

Active 모드는 FTP 연결 수립 후 데이터 전송을 위한 포트로 서버가 클라이언트로 직접 연결 요청을 하는 방식이고 Passive 모드는 FTP 연결 수립 후 서버한테 받은 데이터 전송을 위한 포트 번호로 연결하는 것이다. (보통 Passive 모드로 사용한다.)

#### 알아야 할 것

FTP는 하나의 포트를 사용해서만 통신하지 않는다. (기본 포트는 21번이지만 다른 용도로 다른 포트를 사용한다.)

FTP는 서버와 클라이언트를 사용한다. FTP 서버에 접속하기 위한 클라이언트로 파일질라를 가장 많이 또 널리 사용한다.

근데 포트를 21번을 사용하라 했는데 하나의 포트로 운용이 가능한가? 데이터 송수신을 위한 포트를 하나 더 열어야 할 것으로 보인다.

ftp는 주로 vsftpd (Very Secure FTP Daemon)으로 서버를 구성한다.

vsftpd는 vsftpd.conf 설정대로 동작한다.

## 도커 이미지 생성

쿠버네티스 클러스터를 구성하기 전 구성 요소가 되는 도커 이미지를 생성해 보기로 했다.

### 필요한 이미지

- FTPS 서버 (21)
- phpmyadmin 서버 (5000)
- nginx 서버 (80/443/22)
- 워드프레스 서버 (5050)
- Grafana 서버 (3000)
- MySQL 서버 (내부)
- InfluxDB 서버 (내부)

### alpine linux와 docker

#### Why alpine?

alpine 리눅스는 용량이 아주 가볍다. 따라서 컨테이너의 경량화를 위해 알파인 리눅스를 종종 사용한다. 보통 컨테이너용 운영체제로 많이 사용된다고 한다.

알파인 리눅스에서는 패키지 관리자로 apt 대신 apk를 사용한다.

알파인 리눅스는 데몬 서비스 관리 명령어가 기본으로 존재하지 않는다. (Service) 따라서 데몬을 실행하거나 관리하려면 수동으로 해야 하는데 서비스를 관리해주는 유틸을 설치한다고 해도 정상적으로 사용되지 않는다. 또 보통 알파인 리눅스에는 하나의 서비스만 설치하니 취지에도 맞지 않으므로 수동으로 서비스를 구동시킨다. 

#### docker shell 테스트 환경

alpine linux의 이미지를 가져온다. 태그의 latest는 알아서 최신 버전을 가져오라는 태그다.

가져왔으면 쉘을 테스트 하기 위한 컨테이너를 실행한다. docker에 있는 alpine linux는 기본 쉘로 bash가 아닌 sh를 사용한다.

--rm 옵션을 붙으면 종료된 컨테이너를 알아서 삭제해주므로 사용하기 매우 편해진다.

```sh
$> docker pull alpine:latest
latest: Pulling from library/alpine
596ba82af5aa: Pull complete
Digest: sha256:d9a7354e3845ea8466bb00b22224d9116b183e594527fb5b6c3d30bc01a20378
Status: Downloaded newer image for alpine:latest
docker.io/library/alpine:latest
$> docker run -it --rm alpine:latest sh
/ #
```

### alpine linux에서 FTP 서버 구성

alpine 리눅스에 ftp 설치하는건 구글링을 통해 설치하면 된다.

alpine linux shell 실행은 다음과 같다. (run에 --rm 옵션은 해당 컨테이너를 종료하거나 나가면 자동으로 컨테이너를 제거한다.)

FTP 기본 (명령어) 포트는 21번, FTP 데이터 송수신 포트는 42420 포트를 사용한다. (데이터 송수신 포트는 임의로 정해도 상관없다.)

```sh
$> docker run -it --rm -p 21:21 -p 42420:42420 alpine:latest sh
/ #
```

ftp 서버 (vsftpd)를 구동하는 데 필요한 설정파일 vsftpd.conf 를 작성해본다. (SSL 인증서는 ft_server에서 만든 것과 동일한 방법으로 만든다.)

```
# 익명 연결 금지
anonymous_enable=NO
# 로컬 계정으로 ftp 접속을 가능하게 함
local_enable=YES
# 사용자 홈폴더에서만 접속할 수 있게 함.
chroot_local_user=YES
seccomp_sandbox=NO
allow_writeable_chroot=YES
# 기본적인 업로드 허용
write_enable=YES
# 업로드 되는 파일 권한 설정 (생성되는 파일권한 = 777 - local_umask)
local_umask=022
# 외부에서 연결요청 받아들임
listen=YES
# 패시브 연결을 기본값으로
pasv_enable=YES
# 패시브 연결에 할당될 수 있는 아이피 최소값~최댓값 범위
pasv_min_port=42420
pasv_max_port=42420
# 패시브 연결에 사용될 아이피
pasv_address=127.0.0.1
# SSL (FTPS) 사용
ssl_enable=YES
# SSL 인증서
rsa_cert_file=/certs/cert.crt
# SSL 키
rsa_private_key_file=/certs/key.key
```

한번 ftp 서버를 구동해본다. ftp 서버 접속은 filezilla를 내려받아 사용한다.

```sh
/ # apk --no-cache add vsftpd
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/community/x86_64/APKINDEX.tar.gz
...
OK: 7 MiB in 17 packages
/ # adduser joopark
Changing password for joopark
New password:
Bad password: similar to username
Retype password:
passwd: password for joopark changed by root
/ # mkdir certs
/ # cd certs/
/certs # vi key.key
/certs # vi cert.crt
/certs # cd ..
/ # vi vsftpd.conf
/ # vsftpd vsftpd.conf
```

service 명령어로 ftp 서버를 구동시키는 것이 아닌 수동으로 vsftp 서버를 구동시킨다.

filezilla로 접속해 ssl 인증 관련 창이 뜨고 정상적으로 파일 업/다운로드가 된다면 기본적인 ftp 서버는 구성한것이다.

접속 아이디/비밀번호는 위에서 생성한 계정의 아이디/비밀번호로 한다.

### alpine linux에서 MySQL 서버 구성

ft_server때와 기본적인 설정 방식은 유사하다. 하지만 설치환경이 다르므로 설치 및 실행은 다르다.

검색에 의하면 MySQL은 TCP 통신을 기반으로 DB를 사용하는 프로그램들과 통신한다고 한다. (phpmyadmin/wordpress 등등) 그래서 통신에 사용할 포트를 먼저 개방해야 한다.

```sh
$> docker run -it --rm -p 21:21 -p 3306:3306 alpine:latest sh
/ #
```

apk로 mysql 서버를 먼저 설치한 후 mysql_install_db 명령어로 사용 전 초기 설정을 먼저 진행한다.

mysql_install_db는 mysql 처음 실행시 초기화를 해주는 역할을 해주는 쉘 파일이라고 한다. 인수 --root는 mysql을 실행할 사용자를 지정하는 것이고 --datadir은 실제 DB가 존재하는 디렉토리를 정하는 것이다.

mysql 서버는 mysqld_safe 명령어를 이용해 실행한다고 한다. mysqld_safe는 mysql 서버를 실행시키기 위한 쉘 파일이라고 한다. 인수는 위와 같다.

mysqld는 MySQL Daemon의 약자로 우분투처럼 service 명령어를 이용해 데몬 (실행중인 서비스)을 실행시키지 않고 직접 실행하므로 mysqld를 직접 실행시키며 mysqld_safe은 이를 쉽게 실행하게 해주는 쉘 스크립트이다.

```sh
/ # apk --no-cache add mysql
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/community/x86_64/APKINDEX.tar.gz
...
OK: 150 MiB in 30 packages
/ # mysql_install_db --user=root --datadir=/var/lib/mysql
Installing MariaDB/MySQL system tables in '/var/lib/mysql' ...
OK

To start mysqld at boot time you have to copy
support-files/mysql.server to the right place for your system


Two all-privilege accounts were created.
One is root@localhost, it has no password, but you need to
be system 'root' user to connect. Use, for example, sudo mysql
The second is root@localhost, it has no password either, but
you need to be the system 'root' user to connect.
After connecting you can set the password, if you would need to be
able to connect as any of these users with a password and without sudo

See the MariaDB Knowledgebase at https://mariadb.com/kb or the
MySQL manual for more instructions.

You can start the MariaDB daemon with:
cd '/usr' ; /usr/bin/mysqld_safe --datadir='/var/lib/mysql'

You can test the MariaDB daemon with mysql-test-run.pl
cd '/usr/mysql-test' ; perl mysql-test-run.pl

Please report any problems at https://mariadb.org/jira

The latest information about MariaDB is available at https://mariadb.org/.
You can find additional information about the MySQL part at:
https://dev.mysql.com
Consider joining MariaDB's strong and vibrant community:
https://mariadb.org/get-involved/

/ # mysqld_safe --user=root --datadir=/var/lib/mysql
```

### alpine linux에서 phpmyadmin 구성

컨테이너 기준으로 외부에 있는 db와 통신할 것이므로 MySQL 포트를 개방시키고 phpmyadmin은 5000번 포트를 이용해 유저와 통신할 것이므로 두 포트를 개방시킨다.

```sh
$> docker run -it --rm -p 5000:5000 -p 3306:3306 alpine:latest sh
```

근데 이렇게 실행시키면 3306 포트는 MySQL 구동중인 컨테이너가 사용중이라고 하며 실행되지 않는다.

쿠버네티스에 생성된 이미지들을 올리기 전에 환경 구성 용으로 지금 작업을 하는 것이기 때문에 테스트 용으로 컨테이너끼리 통신할 방법을 검색해본다.

보통 도커는 이미지끼리 네트워크를 공유한다 한다. 그래서 컨테이너마다 각자 서로 다른 사설 아이피가 할당된다고 한다. 이를 고정으로 직접 설정할 수도 있지만 현재 과정은 테스트 과정이므로 그럴 필요는 없고 컨테이너끼리 아이피만 알고 있으면 통신이 가능하다 하고 MySQL 서버는 사용자와 직접 통신할 필요 없으므로 3306 포트를 지운다.

```sh
$> docker run -it --rm -p 5000:5000 alpine:latest sh
/ #
```

일단 기존에 ft_server를 구동시킬 때를 참조해 필요해 보이는 패키지부터 먼저 설치해본다.

```sh
/ # apk --no-cache add nginx php7-mysqli php7-fpm
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/community/x86_64/APKINDEX.tar.gz
...
Executing busybox-1.32.1-r0.trigger
OK: 15 MiB in 28 packages
```

우분투에서는 /etc/nginx/sites-available/default 파일을 수정해 nginx 서버 설정을 하였다. 하지만 해당 파일이 존재하지 않는다. 그러면 저 파일을 include 하는 nginx.conf 파일을 찾아본다.

```sh
/ # find / -name "nginx.conf"
./etc/nginx/nginx.conf
```

일단 nginx/sites-available/default 파일이 무슨 역할을 하는지 확실하게 집고 넘어가야 할 필요성을 느꼇다. ft_server 때는 그냥 메뉴얼대로 셋팅했었는데 이렇게 환경이 바뀔대마다 대응을 못하는 문제를 스스로 느꼇기 때문이다.

인터넷 검색 결과 일단 nginx.conf 파일은 nginx 서버의 환경설정 파일이다.

sites-available 폴더는 nginx가 운용할 가상 호스트 설정이 저장되는 경로라고 한다. 일단 가상 호스트가 무엇인지 설명하기 전에 사용 예부터 설명하자면 웹 서버(WAS 말고 nginx나 아파치 같은거)는 다른 기능들도 있지만 보통 정적 파일을 클라이언트로 전달하거나 리버스 프록시로 사용하기 위해 사용한다.

ft_server에서는 주로 정적 파일을 클라이언트로 전달하는 용도로 썻다. 하지만 지금은 문제 요구사항에 리버스 프록시로 (현재 phpmyadmun에서 리버스 프록시를 사용할 것은 아니지만) 사용하라는 요구사항이 있기 때문에 리버스 프록시로 사용하는 용례도 알아야 한다.

일단 웹서버는 보통 용도로는 정적 파일 (html 자바스크립트 사진 등등등)을 단순히 클라이언트로 사용한다. 하지만 서버가 제공해주는 기능이 많을수록 서버를 여러개 두고 특정 기능은 특정 서버가 담당하도록 둔다. 회사 업무가 많아질수록 팀 인원이 많아져야 하는것과 비슷하다.

이럴 때 리버스 프록시를 사용한다. 리버스 프록시는 외부의 요청을 받으면 그 요청을 내부의 특정 서버로 전달해주는 역할만을 한다. 이렇게 하면 서버를 여러개로 분할하기도 좋고 보안면에서도 좋다. 왜냐 하면 외부에서 보면 하나의 서버가 여러 기능들을 서비스하는 것처럼 보이기 때문이다.

리버스 프록시에 대한 상세한 개념과 설정은 추후에 다시 해야 하니 일단 넘어가고 파일 전달, 프록시 등 현재 nginx 서버의 특정 경로나 포트로 접속하면 동작되는 규칙 등에 대해 설정을 하는 것을 가상 호스트를 설정한다고 한다. nginx.conf의 "http {}" 중괄호 내의 "server {}" 중괄호에서 설정하는것이 보통이다.

예를 들면 이런식이다.

```
# nginx.conf

http {
    server {
        root /var/www/html/page1;
        listen 80;
        index index.php;
        location / {
            try_files $uri $uri/ =404;
        }
    }
    server {
        root /var/www/html/page2;
        listen 8080;
        index index.php;
        location / {
            index index.html;
        }
    }
}
```

이런식으로 서버로 들어오는 요청을 포트별로 혹은 도메인별로 구분해 처리할 수 있다. 하지만 nginx.conf 파일 내에 이 내용을 일일히 기입해 놓으면 관리가 불편하다. 따라서 기본적으로 가상호스트 설정을 다른 경로로부터 Include 하도록 되어있다.

찾아낸 nginx.conf 파일을 보면 http 블록 내에 다음과 같은 내용이 있다.

```
        # Includes virtual hosts configs.
        include /etc/nginx/http.d/*.conf;
```

가상 호스트 설정을 /etc/nginx/http.d 경로 내의 확장자가 conf 인 파일로부터 끌어 쓰겠다 라는 의미이다.

/etc/nginx/http.d 경로 내에는 포트 80번 기본 파일밖에 존재하지 않는다.

기존 php 설정을 할 때의 경험을 되살려 포트 5000번을 대상으로 한 가상 프록시 파일을 새로 작성해 보았다.

```
server {
    listen 5000;
    root /html;
    autoindex on;
    index index.html;
    location / {
        try_files $uri $uri/ =404;
    }
}
```

위처럼 작성하고 위 경로에 임의의 파일명 (5000.conf) 으로 저장하고 nginx 서버를 구동시켜 본다.

웹 페이지가 저장되는 경로는 /html 이다.

nginx 서버 구동법은 다음과 같다.

```sh
/ # nginx
nginx: [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)
```

근데 위 명령어를 실행하면 오류가 발생한다.

/run 폴더는 프로그램을 실행할 때 현재 프로세스에 대한 정보를 저장하는 경로이다. (참고로 우분투나 맥은 /var/run 경로가 동일한 역할을 수행한다.) 근데 nginx 서버는 run 예하 폴더에 nginx 폴더가 존재하지 않아 오류가 발생하는 것이라고 한다. 따라서 해당 폴더를 수동으로 생성해야 한다.

```sh
/ # mkdir /run/nginx
/ # nginx
```

실행하면 백그라운드에서 실행되기 때문에 종료하려면 ps -ef 명령어로 프로세스를 찾아 kill 명령어로 종료해야 한다.

백그라운드에서 실행하지 않고 ctrl+c로 종료하게 하려면 다음과 같이 실행한다.

```sh
/ # nginx -g "daemon off;"
```

nginx를 실행하고 웹브라우저에서 http://127.0.0.1:5000 에 접근하면 정상적으로 동작됨을 확인할 수 있다.

이제 php 연동을 해본다.

ft_server를 참조해 php 연동을 하도록 위 프록시 파일에 내용을 추가해 보자. 또 필요없는 것은 제거해 보자.

```
server {
    listen 5000;
    root /html;
    index index.html index.php;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~ \.php$ {
        include fastcgi.conf;
        fastcgi_pass 127.0.0.1:9000;
    }
}
```

PHP-nginx 연동을 위해서는 FastCGI 라는것을 설정해야 한다. (자세한 내용은 구글 검색) 일단 php 파일에 대한 규칙 (location 추가 된 php 어쩌고 하는게 확장자가 php 파일을 다루겠다는 의미이다.)

FastCGI 설정을 위해서 fastcgi.conf 파일을 인클루드 하고 FastCGI 요청을 보낼 경로를 적는다. (fastcgi_pass) 우분투에서 설치할 때는 유닉스 소켓 (궁금하면 구글검색)이 디폴트였지만 여기 운영체제에서는 아이피로 소켓 통신을 한다. 왜 하필 9000번이냐 하면 php 초기설정이 9000번으로 되어있다. (자세한건 구글검색)

위 파일을 저장하고 /html에 php 파일 하나를 써서 정상적으로 php가 구동이 되는지 확인해 본다.

```php
<?php
phpinfo();
?>
```

이후 쉘에서 php 데몬을 실행해야 한다.

```sh
/ # php-fpm7
```

실행하면 알아서 백그라운드에서 구동된다.

이후 다시 nginx 서버를 구동해 위 php 파일로 접속해서 phpinfo 화면이 나오면 정상적으로 구동되는 것이다.

phpmyadmin은 https://www.phpmyadmin.net/downloads/ 에서 최신버전 tar.gz 링크를 복사해서 wget으로 내려받는다.

```sh
/ # wget https://files.phpmyadmin.net/phpMyAdmin/5.0.4/phpMyAdmin-5.0.4-all-languages.tar.gz
...
/ # tar -zxvf phpMyAdmin-5.0.4-all-languages.tar.gz --strip-components=1 -C /html
...
```

내려받고 다시 저 경로로 접속해보자. 접속하면 아무것도 뜨지 않는다. 에러가 발생한 것으로 보인다.

구글 검색 결과 nginx 혹은 php는 에러 로그를 /var/log 경로에 저장한다고 한다.

들어가서 vi로 에러가 발생한 로그를 찾아본다.

찾다 보면 /var/log/nginx/error.log 에 에러가 발생한 것을 볼 수 있는데 해당 에러 메시지를 보자.

```
  thrown in /html/vendor/symfony/polyfill-mbstring/Mbstring.php on line 501" while reading response header from upstream, client: 172.17.0.1, server: , request: "GET / HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "127.0.0.1:5000"
2021/01/26 10:46:05 [error] 32#32: *1 FastCGI sent in stderr: "PHP message: PHP Fatal error:  Uncaught Error: Call to undefined function Symfony\Polyfill\Mbstring\iconv_strpos() in /html/vendor/symfony/polyfill-mbstring/Mbstring.php:501
Stack trace:
#0 /html/vendor/symfony/polyfill-mbstring/bootstrap.php(60): Symfony\Polyfill\Mbstring\Mbstring::mb_strpos()
#1 /html/libraries/classes/Url.php(257): mb_strpos()
#2 /html/libraries/classes/Url.php(208): PhpMyAdmin\Url::getArgSeparator()
#3 /html/libraries/classes/Url.php(171): PhpMyAdmin\Url::getCommonRaw()
#4 /html/libraries/classes/Core.php(765): PhpMyAdmin\Url::getCommon()
#5 /html/libraries/classes/Core.php(338): PhpMyAdmin\Core::linkURL()
#6 /html/libraries/classes/Core.php(367): PhpMyAdmin\Core::getPHPDocLink()
#7 /html/libraries/classes/Core.php(1009): PhpMyAdmin\Core::warnMissingExtension()
#8 /html/libraries/common.inc.php(110): PhpMyAdmin\Core::checkExtensions()
#9 /html/index.php(23): require_once('/html/libraries...')
#10 {main}
  thrown in /html/vendor/symfony/polyfill-mbstring/Mbstring.php on line 501" while reading response header from upstream, client: 172.17.0.1, server: , request: "GET /index.php HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "127.0.0.1:5000"
2021/01/26 10:59:50 [error] 60#60: *3 FastCGI sent in stderr: "PHP message: PHP Fatal error:  Uncaught Error: Call to undefined function Twig\json_encode() in /html/vendor/twig/twig/src/ExtensionSet.php:116
Stack trace:
#0 /html/vendor/twig/twig/src/Environment.php(984): Twig\ExtensionSet->getSignature()
#1 /html/vendor/twig/twig/src/Environment.php(703): Twig\Environment->updateOptionsHash()
#2 /html/vendor/twig/twig/src/Environment.php(128): Twig\Environment->addExtension()
#3 /html/libraries/classes/Template.php(66): Twig\Environment->__construct()
#4 /html/libraries/classes/Core.php(299): PhpMyAdmin\Template->__construct()
#5 /html/libraries/classes/Core.php(376): PhpMyAdmin\Core::fatalError()
#6 /html/libraries/classes/Core.php(1009): PhpMyAdmin\Core::warnMissingExtension()
#7 /html/libraries/common.inc.php(110): PhpMyAdmin\Core::checkExtensions()
#8 /html/index.php(23): require_once('/html/libraries...')
#9 {main}
  thrown in /html/vendor/twig/twig/src/ExtensionSet.php on line 116" while reading response header from upstream, client: 172.17.0.1, server: , request: "GET / HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "127.0.0.1:5000"
```

보면 iconv_strpos 함수, json_encode 함수가 없다는 오류가 뜬다. (환경에 따라 없는 함수가 더 있을수도 있다.) 구글에 php iconv_strpos undefined 뭐 이런식으로 검색해보면 무슨 확장기능을 설치해야 한다고 나온다.

페이지에 아무것도 안 뜨는 극단적인 경우를 제외하고 보통 접속하면 phpmyadmin 페이지에 무슨 확장기능이 없다고 뜬다.

에러 메시지와 구글링을 통해 추가로 설치해야 하는 확장기능을 설치한다.

```shell
/ # apk --no-cache add php7-mbstring php7-json php7-session
```

일단 로그인 창이 뜨면 db 접속을 시도해 본다.

실행 전 mysql db를 외부에서도 접근할 수 있도록 설정을 바꾸어야 한다. 위 mysql docker 컨테이너에서 해당 설정을 바꾸어야 한다.

[https://gafani.tistory.com/entry/MariaDBMySQL-원격에서-접근이-가능하도록-설정하기](https://gafani.tistory.com/entry/MariaDBMySQL-원격에서-접근이-가능하도록-설정하기) 해당 블로그를 참조하면 mysql을 외부에서 접근할 수 있도록 변경해야 하는 설정이 설정파일에 있다고 한다.

https://wiki.alpinelinux.org/wiki/Production_DataBases_:_mysql 을 참조하면 데이터베이스 설정 파일 경로가 /etc/my.cnf.d/mariadb-server.cnf 에 있다고 한다.

위 블로그에서 말하는 두 가지 요소를 변경하고 저장한다.

이제 데이터베이스를 넣거나 권한 설정 등을 하기 위해 쿼리문을 DB에 입력해야 한다.

mysqld에 --bootstrap 옵션을 넣어서 서버를 실행시키지 않고 쿼리문을 실행시킬 수 있다.

원격에서 접속하기 위한 계정을 하나 만들고 거기에 모든 권한을 할당해 보자. ('%'는 외부에서 연결하는 권한을 허용하겠다는 의미이다. 일종의 와일드카드를 의미하는 듯)

```sql
create user 'joopark'@'%' identified by 'joopark';
grant all privileges on *.* to 'joopark'@'%';
flush privileges;
```

위 쿼리문을 작성후 파일로 저장한 다음 아래 명령을 실행한다.

```sh
/ # cat test.sql | mysqld --datadir=/var/lib/mysql --user=root --bootstrap --skip-grant-tables=0
```

다시 phpmyadmin 컨테이너로 돌아온다. 외부 접속을 하려면 config.inc.php를 변경해야 한다. ft_server 때와 동일하게 config.inc.php를 생성한 다음 내부 변수를 변경한다.

```
$cfg['Servers'][$i]['host'] = '172.17.0.2:3306';
$cfg['blowfish_secret'] = '{^QP+-(3mlHy+Gd~FE3mN{gIATs^1lX+T=KVYv{ubK*U0V';
```

아이피 값은 sql이 구동되고 있는 컨테이너에서 ifconfig 명령어를 이용해 아이피를 알아낸다. 포트 번호는 sql 포트번호이다.

blowfish_secret은 추후에 어차피 채워야 하므로 지금 채운다.

이제 phpmyadmin에 접속하고 위 계정으로 로그인하면 로그인이 될것이다.

이제 초기화면 밑 경고 메세지들에 대해 조치하면 된다.

### wordpress 구성

5050 포트를 사용한다.

```sh
$> docker run -it --rm -p 5050:5050 alpine:latest sh
/ #
```

동일하게 php 위에서 구동되므로 먼저 php, nginx를 설치한다.

```shell
/ # apk --no-cache add nginx php7-mysqli php7-fpm
```

이전처럼 php 확장기능이 설치되지 않아 정상적으로 구동되지 않은 문제가 있어서 이번엔 미리 wordpress를 구동하려면 필요한 php 확장기능이 무엇이 있는지 검색해본다.

구글에 "wordpress php extension requirements" 와 같이 검색해 보면

```
sudo apt install php7.3-common php7.3-mysql php7.3-curl php7.3-json php7.3-mbstring php7.3-xml php7.3-zip php7.3-gd php7.3-soap php7.3-ssh2 php7.3-tokenizer 
```

우분투에 설치하는 기준으로 다음 패키지를 깔아야 한다고 한다. 위 패키지 이름을 바꿔서 설치해 보자.

```shell
/ # apk --no-cache add php7-common php7-curl php7-json php7-mbstring php7-xml php7-zip php7-gd php7-soap php7-ssh2 php7-tokenizer 
```

wordpress를 내려받고 nginx 설정을 하고 php 데몬 실행한다.

```shell
/ # wget https://ko.wordpress.org/latest-ko_KR.tar.gz
/ # mkdir html
/ # tar -zxvf latest-ko_KR.tar.gz --strip-components=1 -C /html
/ # mkdir /run/nginx
/ # vi /etc/nginx/http.d/wordpress.conf
/ # php-fpm7
/ # cp /html/wp-config-sample.php /html/wp-config.php
/ # dos2unix /html/wp-config.php
/ # vi /html/wp-config.php
/ # nginx -g "daemon off;"
```

dos2unix 명령어는 해당 config 파일에 ^M 문자가 들어가 있어 없애는데 사용하였다. (관련 내용을 구글링 해서 알아냄) 어차피 추후에 wp-config 파일은 별도로 만들어서 삽입할 것이기 때문에 추후엔 필요 없을 명령어이다.

wordpress 관련해 추가로 설정해야 할 것들이 있는 것으로 알고 있는데 추후에 필요하면 하기로 했다.

## Grafana / InfluxDB

참고 자료/링크

```
https://medium.com/naver-cloud-platform/grafana-influxdb를-활용한-모니터링-서비스-구축하기-62de4b07a505
https://musma.github.io/2019/07/08/getting-started-with-influxdb-time-series-database.html
https://www.popit.kr/influxdb_telegraf_grafana_1/
```

### Grafana

서비스를 운영하는 데 현재 상태를 파악하는 것과 자원 등을 얼마나 소모하는지 아는 것은 매우 중요하다. 기존 ft_server에서는 구동되는 서비스가 잘 구동되는지, 자원을 얼마나 소모하는지 별도로 확인할 수 없었다.

Grafana는 오픈소스 기반 메트릭 데이터를 시각화하는 프로그램이다. (메트릭 데이터란 간단히 말하면 분야/항목을 의미하는 것이다. 예를 들면 프로그램을 실행할 때 프로그램이 먹는 메모리 양, cpu 점유율 등등 이런 것들을 메트릭이라고 부를 수 있다.)

Grafana를 운용하는 방식은 다양한데 grafana와 InfluxDB를 연동하여 많이 사용하고 telegraf 라는 프로그램으로 메트릭 데이터를 간편하게 수집할 수 있다고 한다.

Grafana가 서비스를 제공하는 위 컨테이너의 메트릭 데이터를 수집해서 (실제 수집은 InfluxDB에서 하지만) 시각화 해서 실시간으로 보여주는 역할을 할 것이다.

### InfluxDB

InfluxDB는 데이터를 실시간으로 시간 순서에 따라 저장하고 조회하는 데 특화된 데이터베이스이다. (이런 데이터베이스를 시계열 데이터베이스(TSDB)라고 한다고 한다.)

MySQL / Oracle DB / PostgreSQL 같은 DBMS와의 차이점은 시간 값을 키 인덱스로 저장하는 부분이 가장 다르다. (예를 들어 현재 시간 2021년 1월 27일 오후 8시 39분 22초 이 순간에 대한 어떤 데이터를 가져오는 것에 대한 것. DBMS에서 이런 행동을 못하는건 아니지만 이렇게 시간별 데이터 저장은 TSDB에 특화되어있다.)

## InfluxDB 설치

InfluxDB의 기본 데이터 통신 포트는 두 가지가 있다고 한다. 데이터 송수신은 8086을 이용하며 웹브라우저로 데이터베이스를 관리할 수 있는데 이 때 사용되는 포트는 8083이다. 두개의 포트를 개방하고 컨테이너를 실행해본다.

```sh
$> docker run -it --rm -p 8083:8083 -p 8086:8086 alpine:latest sh
/ #
```

influxdb 패키지를 설치해본다.

```shell
/ # apk --no-cache add influxdb
```

influxdb를 설치하면 influxdb 프로그램 자체는 서버에 접속하는 클라이언트라고 한다. 그럼 서버 프로그램은 무엇일까? tab 키를 눌러가면서 influx로 지삭하는 프로그램이 무엇이 있는지 찾아봤다.

```shell
/ # influx
influx          influx_inspect  influx_stress   influx_tools    influx_tsm      influxd
```

"influxd"가 있다. 아무래도 mysql과 mysqld와 같은 관계인거 같다. 아무래도 influx daemon인것 같다.

해당 프로그램에 보통 프로그램 도움말 인수를 의미하는 "-h" , "--help" 를 입력해본다.

```shell
/ # influxd -h
Configure and start an InfluxDB server.

Usage: influxd [[command] [arguments]]

The commands are:

    backup               downloads a snapshot of a data node and saves it to disk
    config               display the default configuration
    help                 display this help message
    restore              uses a snapshot of a data node to rebuild a cluster
    run                  run node with existing configuration
    version              displays the InfluxDB version

"run" is the default command.

Use "influxd [command] -help" for more information about a command.
/ # influxd run -help
Runs the InfluxDB server.

Usage: influxd run [flags]

    -config <path>
            Set the path to the configuration file.
            This defaults to the environment variable INFLUXDB_CONFIG_PATH,
            ~/.influxdb/influxdb.conf, or /etc/influxdb/influxdb.conf if a file
            is present at any of these locations.
            Disable the automatic loading of a configuration file using
            the null device (such as /dev/null).
    -pidfile <path>
            Write process ID to a file.
    -cpuprofile <path>
            Write CPU profiling information to a file.
    -memprofile <path>
            Write memory usage information to a file.

run: flag: help requested
```

역시 추측대로 influxd는 서버 프로그램이었다. 사용법을 참조하여 서버를 실행시켜 본다.

인터넷에 따르면 influxdb.conf가 설정파일이라고 한다. 일단 이 파일을 찾아서 실행시켜 보자.

```shell
/ # find . -name "influxdb.conf"
./etc/influxdb.conf
/ # influxd run -config /etc/influxdb.conf

 8888888           .d888 888                   8888888b.  888888b.
   888            d88P"  888                   888  "Y88b 888  "88b
   888            888    888                   888    888 888  .88P
   888   88888b.  888888 888 888  888 888  888 888    888 8888888K.
   888   888 "88b 888    888 888  888  Y8bd8P' 888    888 888  "Y88b
   888   888  888 888    888 888  888   X88K   888    888 888    888
   888   888  888 888    888 Y88b 888 .d8""8b. 888  .d88P 888   d88P
 8888888 888  888 888    888  "Y88888 888  888 8888888P"  8888888P"

2021-01-27T14:03:05.280643Z	info	InfluxDB starting
... 중략 ...
```

뭔가 실행이 되긴 한다.

InfluxDB Web UI에 접속해보려 했는데 접속이 안된다. 알아보니 버전 1.1 이후로 해당 기능은 **Deprecated** 되었다고 한다. (출처 : https://github.com/influxdata/influxdata-docker/issues/62)

그러면 그냥 내부 클라이언트 프로그램으로 실행해보기로 했다. 동일한 컨테이너에서 실행할 것이므로 쉘을 하나 더 띄운다.

```sh
$> docker ps
CONTAINER ID   IMAGE           COMMAND   CREATED          STATUS          PORTS                                            NAMES
f9e30cc6e6a0   alpine:latest   "sh"      13 minutes ago   Up 13 minutes   0.0.0.0:8083->8083/tcp, 0.0.0.0:8086->8086/tcp   recursing_burnell
$> docker exec -it f9e3 sh
/ # influx
Connected to http://localhost:8086 version 1.8.3
InfluxDB shell version: 1.8.3
>
```

데이터베이스에 대한 자세한 구조는 구글링을 참조하는게 좋겠지만 간단하게 설명하면

influx의 database - measurement 관계가 MySQL 같은 DBMS에서 database - table 이다.

database 하위에 measurement가 있고 measurement는 여러 데이터 값을 의미한다.

초기엔 데이터가 없을테니 데이터를 집어넣어 본다.

외부 (맥 쉘 등등)에서 아래 명령어를 실행해본다.

```sh
$> curl -i -XPOST 'http://127.0.0.1:8086/query' --data-binary "q=CREATE DATABASE people"
$> curl -i -XPOST 'http://127.0.0.1:8086/write?db=people' --data-binary 'pujuhong,region=korea temp=36,blood_pressure_min=80,blood_pressure_max=120'
```

명령어의 의미는 HTTP 프로토콜 중 POST를 이용해 저 url로 --data-binary의 데이터를 전송하겠다는 의미이다. influxdb는 데이터를 HTTP 프로토콜로 수신받기 때문에 HTTP 프로토콜을 이용해 패킷을 보낼 수 있는 curl을 이용한 것이다. (자세한건 curl 명령어 구글링)

먼저 people이란 데이터 베이스를 생성한다. 이후 데이터를 삽입하는데 pujuhong이라는 measurement에 국가, 체온, 혈압을 삽입한다.

데이터를 삽입하는것을 보면

```
pujuhong,region=korea temp=36,blood_pressure_min=80,blood_pressure_max=120
```

이렇게 되어 있는데 이는

```
[measurement],[태그명]=[태그값][공백][필드명]=[필드값]
```

으로 이루어진 것이다.

InfluxDB는 DBMS에서 테이블에 대응되는 measurement에 대해 초기화나 선언 등을 할 필요 없다. 즉석에서 값을 대입하면 된다.

그러면 태그는 무엇이고 키는 무엇인가?

태그는 현재 컬럼 (DBMS에서 말하는 컬럼. 데이터 한 줄)을 의미한다. InfluxDB는 컬럼을 태그라고 부른다.

필드는 DBMS에서의 의미와 같다.

자세한건 [http://blog.naver.com/alice_k106/221226137412](http://blog.naver.com/alice_k106/221226137412) , [https://musma.github.io/2019/07/08/getting-started-with-influxdb-time-series-database.html](https://musma.github.io/2019/07/08/getting-started-with-influxdb-time-series-database.html) 참조.

아무튼 데이터를 집어넣었으니 열람해보자.

```sh
> show databases
name: databases
name
----
_internal
people
> use people
Using database people
> show measurements
name: measurements
name
----
pujuhong
> select * from pujuhong
name: pujuhong
time                blood_pressure_max blood_pressure_min region temp
----                ------------------ ------------------ ------ ----
1611762474305246900 120                80                 korea  36
>
```

집어넣은 데이터가 열람되는것을 확인할수 있다. 동작은 잘 하는거 같으니 telegraf를 사용해보자.

## telegraf

telegraf는 InfluxDB에서 제작한 프로그램이며 시스템을 모니터링해서 데이터를 InfluxDB 등으로 전송해주는 프로그램이다. (자세한건 구글 검색)

telegraf 말고도 fluentd 등 비슷한 기능을 하는 다른 프로그램들이 있다고 한다.

telegraf는 모니터링을 할 시스템에서 구동시켜 모니터링 내용을 InfluxDB로 전송할 것이므로 현재 실행중인 컨테이너 중 한곳에 먼저 설치해본다.

```shell
/ # apk --no-cache add telegraf
```

[https://www.popit.kr/influxdb_telegraf_grafana_2/](https://www.popit.kr/influxdb_telegraf_grafana_2/) 링크를 참조해서 telegraf를 시범용으로 구동시켜 본다.

일단 telegraf는 설치한 상태이고 ip 주소로 직접 통신할 것이므로 블로그에 설치와 host 설정은 건너뛴다.

telegraf는 수집할 정보 등의 설정을 가지고 있는 telegraf.conf 파일이 있다고 한다. telegraf 스스로 이 설정 파일을 생성할 수 있다고 한다. mysql이 구동되는 환경에서 구동 테스트를 하고 있으므로 블로그에 있는 mysql 설정 그대로 따라간다.

```shell
/ # telegraf -sample-config -input-filter cpu:disk:kernel:mem:net:netstat:system:mysql -output-filter influxdb > telegraf.conf
```

cpu, disk, 커널 등등의 정보를 수집하며 mysql 관련 정보도 수집할 것이다. 파일을 생성하면 현재 구동 환경에 맞게 필요한 부분을 수정해야 한다.

vi 편집기로 outputs.influxdb 부분을 찾아서 다음 항목을 수정한다. (찾기 명령어 : /검색어, 다음 찾기 명령어 : n, 이전 찾기 명령어 : N)

```
[[outputs.influxdb]]
...
 urls = ["http://[Influx 컨테이너 아이피]:8086"]
...
 database = "mysql"
```

이렇게 일단 당장 구동에 필요한 부분만 수정하고 Influx에 정상적으로 데이터가 쌓이는지 확인한다.

블로그에서는 telegraf를 서비스 명령어로 구동되게 하였지만 현재 환경에서 불가능하므로 InfluxDB를 구동시켰을 때와 동일한 방법으로 구동시켜 본다.

telegr까지 타이핑 했을때 실행 가능한 프로그램이 telegraf밖에 없다.

```shell
/ # telegraf
/ # telegraf --help
Telegraf, The plugin-driven server agent for collecting and reporting metrics.

Usage:

  telegraf [commands|flags]

The commands & flags are:

  config              print out full sample configuration to stdout
  version             print the version to stdout

  --aggregator-filter <filter>   filter the aggregators to enable, separator is :
  --config <file>                configuration file to load
  --config-directory <directory> directory containing additional *.conf files
  --plugin-directory             directory containing *.so files, this directory will be
                                 searched recursively. Any Plugin found will be loaded
                                 and namespaced.
  --debug                        turn on debug logging
  --input-filter <filter>        filter the inputs to enable, separator is :
  --input-list                   print available input plugins.
  --output-filter <filter>       filter the outputs to enable, separator is :
  --output-list                  print available output plugins.
  --pidfile <file>               file to write our pid to
  --pprof-addr <address>         pprof address to listen on, don't activate pprof if empty
  --processor-filter <filter>    filter the processors to enable, separator is :
  --quiet                        run in quiet mode
  --section-filter               filter config sections to output, separator is :
                                 Valid values are 'agent', 'global_tags', 'outputs',
                                 'processors', 'aggregators' and 'inputs'
  --sample-config                print out full sample configuration
  --once                         enable once mode: gather metrics once, write them, and exit
  --test                         enable test mode: gather metrics once and print them
  --test-wait                    wait up to this many seconds for service
                                 inputs to complete in test or once mode
  --usage <plugin>               print usage for a plugin, ie, 'telegraf --usage mysql'
  --version                      display the version and exit

Examples:

  # generate a telegraf config file:
  telegraf config > telegraf.conf

  # generate config with only cpu input & influxdb output plugins defined
  telegraf --input-filter cpu --output-filter influxdb config

  # run a single telegraf collection, outputting metrics to stdout
  telegraf --config telegraf.conf --test

  # run telegraf with all plugins defined in config file
  telegraf --config telegraf.conf

  # run telegraf, enabling the cpu & memory input, and influxdb output plugins
  telegraf --config telegraf.conf --input-filter cpu:mem --output-filter influxdb

  # run telegraf with pprof
  telegraf --config telegraf.conf --pprof-addr localhost:6060
/ # telegraf --config telegraf.conf
2021-01-28T02:40:38Z I! Starting Telegraf 1.17.0
2021-01-28T02:40:38Z I! Loaded inputs: cpu disk kernel mem mysql net netstat system
2021-01-28T02:40:38Z I! Loaded aggregators:
2021-01-28T02:40:38Z I! Loaded processors:
2021-01-28T02:40:38Z I! Loaded outputs: influxdb
2021-01-28T02:40:38Z I! Tags enabled: host=adf2d78c04f7
2021-01-28T02:40:38Z I! [agent] Config: Interval:10s, Quiet:false, Hostname:"adf2d78c04f7", Flush Interval:10s

```

구동시키면 InfluxDB로 데이터를 전송한다. 다시 InfluxDB의 쉘로 돌아와 DB를 조회해본다.

```sh
> show databases
name: databases
name
----
_internal
people
mysql
> use mysql
Using database mysql
> show measurements
name: measurements
name
----
cpu
disk
kernel
mem
mysql
net
netstat
system
> select * from kernel
name: kernel
time                boot_time  context_switches entropy_avail host         interrupts processes_forked
----                ---------  ---------------- ------------- ----         ---------- ----------------
1611801640000000000 1611397030 395734441        3185          adf2d78c04f7 231201864  229577
1611801650000000000 1611397030 395741523        3187          adf2d78c04f7 231206364  229580
1611801660000000000 1611397030 395750344        3190          adf2d78c04f7 231211699  229588
1611801670000000000 1611397030 395760634        3192          adf2d78c04f7 231217933  229595
1611801680000000000 1611397030 395770884        3195          adf2d78c04f7 231223879  229596
1611801690000000000 1611397030 395782705        3197          adf2d78c04f7 231230325  229596
...
```

일단 데이터는 잘 쌓이는 것으로 보인다. 이 데이터를 Grafana와 연동해보자.

### Grafana 설치 및 실행

ft_service에서는 grafana를 3000번 포트를 이용해서 접근할 수 있도록 하라고 하였으니 3000번 포트를 개방한다.(grafana는 기본적으로 3000 포트를 사용한다고 한다.) 또 실행중인 컨테이너가 너무 많아 이름을 붙이기로 했다.

```sh
$> docker run -it --rm --name grafana -p 3000:3000 alpine:latest sh
/ #
```

grafana를 설치하고 실행한다. 역시나 service 명령어가 없으므로 직접 데몬을 찾아서 도움말을 본 후 실행한다.

```sh
/ # apk --no-cache add grafana
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/community/x86_64/APKINDEX.tar.gz
(1/1) Installing grafana (7.3.6-r0)
Executing grafana-7.3.6-r0.pre-install
Executing busybox-1.32.1-r0.trigger
OK: 171 MiB in 15 packages
/ # grafana-
grafana-cli     grafana-server
/ # grafana-server --help
Usage of grafana-server:
  -config string
    	path to config file
  -convey-json
    	When true, emits results in JSON blocks. Default: 'false'
  -convey-silent
    	When true, all output from GoConvey is suppressed.
  -convey-story
    	When true, emits story output, otherwise emits dot output. When not provided, this flag mirrors the value of the '-test.v' flag
  -homepath string
    	path to grafana install/home path, defaults to working directory
  -packaging string
    	describes the way Grafana was installed (default "unknown")
  -pidfile string
    	path to pid file
  -profile
    	Turn on pprof profiling
  -profile-port uint
    	Define custom port for profiling (default 6060)
  -tracing
    	Turn on tracing
  -tracing-file string
    	Define tracing output file (default "trace.out")
  -v	prints current version and exits
/ # grafana-server
Grafana-server Init Failed: Could not find config defaults, make sure homepath command line parameter is set or working directory is homepath
/ #
```

config 파일이 없어서 실행이 되지 않는다고 한다. config 파일이 어디있는지 알아본다.

[https://grafana.com/docs/grafana/latest/administration/configuration/](https://grafana.com/docs/grafana/latest/administration/configuration/)

defaults.ini 라는 파일이 설정파일이라고 한다. 이 파일 경로를 찾아본다.

```sh
/ # find . -name "defaults.ini"
./usr/share/grafana/conf/defaults.ini
/ # grafana-server -config /usr/share/grafana/conf/defaults.ini
Grafana-server Init Failed: Could not find config defaults, make sure homepath command line parameter is set or working directory is homepath
```

다시 보니까 honepath를 지정하란 오류였다. 저 사이트를 보면 /usr/share/grafana가 homepath인 것으로 보인다. 다시 실행해본다.

```sh
/ # grafana-server -homepath /usr/share/grafana
INFO[01-28|03:16:34] Starting Grafana                         logger=server version=7.3.6 commit=f25d63954b branch=master compiled=2020-12-17T09:54:28+0000
INFO[01-28|03:16:34] Config loaded from                       logger=settings file=/usr/share/grafana/conf/defaults.ini
INFO[01-28|03:16:34] Path Home                                logger=settings path=/usr/share/grafana
INFO[01-28|03:16:34] Path Data                                logger=settings path=/usr/share/grafana/data
INFO[01-28|03:16:34] Path Logs                                logger=settings path=/usr/share/grafana/data/log
INFO[01-28|03:16:34] Path Plugins                             logger=settings path=/usr/share/grafana/data/plugins
INFO[01-28|03:16:34] Path Provisioning                        logger=settings path=/usr/share/grafana/conf/provisioning
...
```

실행이 된다. 3000번 포트로 접속해보자.

접속하면 로그인하라는 창이 뜬다. 기본 아이디와 pw는 admin/admin이다. 로그인해보자.

로그인해서 다른 블로그 등을 참조해 InfluxDB를 연동시키고 대쉬보드에서 원하는 로그값을 그래프 형식으로 열람할 수 있다.

### nginx 서버

nginx 서버의 요구사항은

- 포트 80으로 들어오는 요청은 443으로 리다이렉션
- /wordpress 로 접근할 시 307 리다이렉트
- /phpmyadmin 으로 접근할 시 리버스 프록시
- ssh로 접근가능

가 있다.

ssh는 시큐어 쉘의 약자이며 원격으로 쉘을 통해 통신할 때 사용하는 프로토콜이다.

ssh로 서버에 접속하려면 서버 내에 ssh 서버가 구동중이어야 한다.

일단 nginx 서버 및 ssh 서버를 설치해보자.

```sh
$> docker run -it --rm -p 80:80 -p 443:443 -p 22:22 -p 5000:5000 -p 5050:5050 alpine:latest sh
/ # apk --no-cache add nginx openssh
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/main/x86_64/APKINDEX.tar.gz
fetch https://dl-cdn.alpinelinux.org/alpine/v3.13/community/x86_64/APKINDEX.tar.gz
...
OK: 13 MiB in 25 packages
/ #
```

일단 ssh 서버부터 구동시켜보자.

```sh
/ # sshd
sshd re-exec requires execution with an absolute path
/ # find . -name "sshd"
./usr/sbin/sshd
./etc/conf.d/sshd
./etc/init.d/sshd
/ # /usr/sbin/sshd
sshd: no hostkeys available -- exiting.
```

ssh는 "secure" shell 이므로 보안 관련 요소를 설정해야 한다.

ssh는 ssh key라는 것이 존재하며 이는 클라이언트와 서버가 통신하는 동안 중간에 내용을 가로채지 못하게 하는 역할을 한다.

ssh key는 서버 측이 클라이언트에게 공개키를 전달하는 방식이기 때문에 서버 내에 키가 존재해야 한다.

ssh key는 ssh-keygen이라는 것을 이용해서 만든다.

ssh key는 다음과 같이 생성한다.

```sh
/ # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''
Generating public/private rsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_rsa_key
Your public key has been saved in /etc/ssh/ssh_host_rsa_key.pub
The key fingerprint is:
SHA256:Wtnb9iyniMN4v8cXwNNmHhHLDnNfuArhgrUr/riy3Ok root@98b40ef287fd
The key's randomart image is:
+---[RSA 3072]----+
|              .. |
|             ..o |
|        . ..o.=..|
|       o = .+==o.|
|      . S +  *o..|
|       o o + .o  |
|      oo. ..+  . |
|   ..o.++. o+oo  |
|    o+E+oo+o.=o  |
+----[SHA256]-----+
```

인수에 대한 설명은 다음과 같다.

- -t : 만들 암호쌍의 알고리즘을 정함
- -f : 저장할 암호쌍의 경로를 정함
- -N : 암호쌍에 대한 비밀번호를 입력하게 되어 있는데 비밀번호는 사용하지 않는다.

암호쌍을 생성한 이후로 다시 데몬을 실행해보자.

```sh
/ # /usr/sbin/sshd
/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 sh
   23 root      0:00 sshd: /usr/sbin/sshd [listener] 0 of 10-100 startups
   24 root      0:00 ps
/ #
```

이제 이 컨테이너에 ssh로 접근해본다. 로컬 쉘에서 접속해보자.

```sh
$> ssh root@127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
RSA key fingerprint is SHA256:Wtnb9iyniMN4v8cXwNNmHhHLDnNfuArhgrUr/riy3Ok.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '127.0.0.1' (RSA) to the list of known hosts.
root@127.0.0.1's password:
Permission denied, please try again.
root@127.0.0.1's password:
Permission denied, please try again.
root@127.0.0.1's password:
root@127.0.0.1: Permission denied (publickey,password,keyboard-interactive).
```

현재 컨테이너 root 계정에 대해 쉘로 접근할 수 있는 권한 설정을 해야 한다. 이는 ssh 설정파일로 변경한다.

sshd_config 파일의 #PermitRootLogin prohibit-password 구간을 주석을 제거하고 PermitRootLogin yes 로 변경해야 한다.

추가로 root 계정에 대한 비밀번호도 설정해야 한다.

리눅스에서 계정 비밀번호를 설정하는데에는 크게 두가지 방법이 있다.

- passwd
- chpasswd

chpasswd는 쉘 등을 이용해 자동화하기 편하므로 현재 상황에선 chpasswd를 사용한다.

```sh
/ # vi /etc/ssh/sshd_config
/ # echo 'root:joopark' | chpasswd
chpasswd: password for 'root' changed
```

이후 ssh 데몬을 종료한 후 재시작 해보자.

```sh
/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 sh
   23 root      0:00 sshd: /usr/sbin/sshd [listener] 0 of 10-100 startups
   33 root      0:00 ps
/ # kill 23
/ # /usr/sbin/sshd
```

다시 ssh로 접근해본다. 접속이 잘 된다.

```sh
$> ssh root@127.0.0.1
root@127.0.0.1's password:
Welcome to Alpine!

The Alpine Wiki contains a large amount of how-to guides and general
information about administrating Alpine systems.
See <http://wiki.alpinelinux.org/>.

You can setup the system with the command: setup-alpine

You may change this message by editing /etc/motd.

98b40ef287fd:~#
```

이제 nginx를 건드려보자. 위해서 조금 했듯이 리버스 프록시 등 가상 호스트 설정을 바꾸려면 /etc/nginx/http.d 경로에 설정을 삽입해야 한다.

기존에 https 적용했던 경험을 살려 https를 적용해본다.

```sh
/ # mkdir cert
/ # mkdir html
/ # vi /cert/cert.crt
/ # vi /cert/key.key
/ # vi /html/index.html
/ # rm /etc/nginx/http.d/default.conf
/ # vi /etc/nginx/http.d/http.conf
/ # vi /etc/nginx/http.d/https.conf
```

http와 https 파일은 각각 다음과 같이 써 넣는다.

```
server {
    listen 80;
    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    ssl_certificate /cert/cert.crt;
    ssl_certificate_key /cert/key.key;
    ssl_session_timeout 5m;
    ssl_prefer_server_ciphers on;
    index index.html index.htm;
    root /html;

    location / {
        try_files $uri $uri/ =404;
    }

    location /wordpress {
        return 307 http://172.17.0.3:5050;
    }

    location /phpmyadmin {
        proxy_pass http://172.17.0.3:5000;
    }
}
```

위에서 172.17.0.3 이라는 컨테이너 url만 적당히 바꿔주면 된다.

지금까지 쿠버네티스를 제외한 기본적인 기능 구현은 전부 한 것으로 보인다. 세부 설정은 필요할 때 수행하도록 한다.

## 다시 쿠버네티스로

운용에 필요한 기본적인 이미지 생성 (명확하게 하진 않았지만)은 가능하도록 준비 작업을 해 보았다. 다시 쿠버네티스로 돌아와보자.

일단 처음에 뭐가 뭔지 잘은 모르지만 쿠버네티스 클러스터를 생성해 보았다. 하지만 아직 뭐가 뭔지 감이 오지 않는다.

### minikube

쿠버네티스 클러스터 환경을 만들기 위해 먼저 minikube를 이용해 클러스커를 구동시킨다.

```shell
$> minikube start
😄  Darwin 11.1 위의 minikube v1.17.0
✨  자동적으로 docker 드라이버가 선택되었습니다. 다른 드라이버 목록: hyperkit, ssh
👍  minikube 클러스터의 minikube 컨트롤 플레인 노드를 시작하는 중
💾  Downloading Kubernetes v1.20.2 preload ...
    > preloaded-images-k8s-v8-v1....: 491.22 MiB / 491.22 MiB  100.00% 35.35 Mi
🔥  Creating docker container (CPUs=2, Memory=1988MB) ...
🐳  쿠버네티스 v1.20.2 을 Docker 20.10.2 런타임으로 설치하는 중
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### kubectl

kubectl은 쿠버네티스 클러스터를 구동, 설정, 상태 확인을 하는데 핵심인 명령어이다. 사용자는 kubectl을 이용해서 쿠버네티스와 통신을 하며 제어한다.

kubectl을 사용하려면 대상 시스템에 쿠버네티스가 설치되어 있어야 한다. (kubectl은 쿠버네티스의 마스터 노드와 통신한다.)

kubectl의 기본적인 명령어를 알아본다.

#### kubectl get

get 명령은 현재 쿠버네티스 클러스터가 운용중인 자원의 상태를 알아낸다.

예를 들면 위에서 설명한 파드, 파드의 복제본들인 레플리카셋, 파드나 레플리카셋이 모여있는 노드, 네트워크를 연결시키는 서비스 등을 조회할 수 있다.

```shell
$> kubectl get pod
$> kubectl get replicaset
$> kubectl get node
$> kubectl get service
...
```

#### kubecel delete

delete 명령은 위에서 조회한 자원을 제거할 수 있는 명령어이다.

```shell
$> kubectl delete pod/[name]
$> kubectl delete replicaset/[name]
$> kubectl delete node/[name]
$> kubectl delete service/[name]
...
```

그 외 exec, config, logs 명령어들이 있다.

### pod 만들어 보기

pod을 만드려면 이미지가 필요하다. 위에서 만들어봤던 여러 컨테이너 중 mysql을 dockerfile로 작성한 다음 이미지로 만들어 본다.

```dockerfile
FROM alpine:latest

COPY query.sql /tmp/init/
COPY mariadb-server.cnf /tmp/init/

RUN apk --no-cache add mysql \
&& mysql_install_db --user=root --datadir=/var/lib/mysql \
&& mv /tmp/init/mariadb-server.cnf /etc/my.cnf.d/ \
&& cat /tmp/init/query.sql | mysqld --datadir=/var/lib/mysql --user=root --bootstrap --skip-grant-tables=0

EXPOSE 3306

CMD ["mysqld_safe", "--user=root", "--datadir=/var/lib/mysql"]
```

```shell
$> docker build -t "ft_services/mysql:0.1" .
```

이후 pod을 생성해 본다.

```shell
$> kubectl run mysql --image=ft_services/mysql:0.1
pod/mysql created
$> kubectl get pod
NAME    READY   STATUS             RESTARTS   AGE
mysql   0/1     ImagePullBackOff   0          56s
```

생성하면 ImagePullBackOff 라는 오류가 발생한다. 왜 그렇냐 하면 kubectl에서는 기본적으로 서버에 있는 이미지를 가져오도록 되어있다. 로컬에 있는 이미지는 접근하지 못해 별도로 조치를 취해야 한다.

구글에 minikube ImagePullBackOff 와 같은 검색어를 이용해 해결법을 찾았다. minikube docker-env 명령어는 로컬의 도커 이미지에 접근할 수 있는 환경변수를 출력해준다. (현재 쿠버네티스 내에서 구동되는 도커에 접근할 수 있도록 하는 것이다.) 따라서 eval $(minikube docker-env) 와 같이 실행하면 환경변수가 set 되며 이미지를 새로 빌드해야 한다.

생성 실패한 pod를 제거하고 다시 생성한다.

```shell
$> kubectl delete pod/mysql
pod "mysql" deleted
$> eval $(minikube docker-env)
$> docker build -t "ft_services/mysql:0.1" [dockerfile 경로]
...중략...
Successfully tagged ft_services/mysql:0.1
$> kubectl run mysql --image=ft_services/mysql:0.1
pod/mysql created
$> kubectl get pod
NAME    READY   STATUS    RESTARTS   AGE
mysql   1/1     Running   0          3s
```

이제 정상적으로 이미지가 구동되는 것을 확인할 수 있다.

위 명령을 동일하게 파일로 명세할 수 있다. 보통 쿠버네티스에서는 설정을 YAML 이라는 파일 형식으로 명세한다.

YAML 형식에 대해선 구글링하면 많이 나오니 한번 찾아보면 좋을것 같다.

위와 동일하지만 상세한 내용을 파일로 작성해보자.

```yaml
apiVersion: v1
kind: Pod # 생성하고자 하는 자원의 종류 (pod 이외에도 레플리카셋 서비스 등이 올수있음)
metadata: # 생성하고자 하는 자원의 메타데이터 (이름, 라벨 등)
  name: mysql # 이름
  labels:
    app: mysql
spec: # 상세한 자원 명세
  containers:
    - name: app
      image: ft_services/mysql:0.1
```

이 yaml 파일을 이용해 pod을 생성하고 삭제해보자.

```shell
$> kubectl apply -f mysql_pod.yml
pod/mysql created
$> kubectl get pod
NAME    READY   STATUS    RESTARTS   AGE
mysql   1/1     Running   0          3s
$> kubectl delete -f mysql_pod.yml
pod "mysql" deleted
```

yaml에 몇가지 명세를 더 추가하면 더 많은 일을 할 수 있다.

예를 들면 컨테이너는 실행하자마자 바로 사용 가능한 상태로 돌입하지 않는다. 초기화 과정이 필요한 컨테이너도 있고 여러 환경에 따라 사용이 불가능한 상태를 유지하다 특정 시간이나 조건이 되면 사용 가능한 상태가 되는 컨테이너도 있을 것이다.

또 컨테이너가 갑자기 deadlock (다르지만 코딩을 하다가 의미없는 무한루프에 빠지면 아무런 동작을 안하는것처럼 deadlock 상태에 빠지면 아무런 동작을 안한다.) 상태에 빠지거나 하는 상황을 감시하는 용도로 주기적으로 컨테이너를 검사할 수 있다.

이러한 것을 판단해서 정상적으로 동작하는지 확인한 다음 정상적으로 동작하지 않을 때 컨테이너를 재시작 할 수 있다.

이 기능은 yaml에 livenessProbe을 추가하여 구현할 수 있다.

readinessProbe는 옵션은 동일하지만 정상 상태가 아니라면 pod으로 들어오는 요청을 제외시킨다. (보통 두개를 같이 사용한다.)

[https://medium.com/finda-tech/kubernetes-pod의-진단을-담당하는-서비스-probe-7872cec9e568](https://medium.com/finda-tech/kubernetes-pod의-진단을-담당하는-서비스-probe-7872cec9e568) 를 참조하면 좋다.

이 기능도 현재는 필요하지 않은 기능이니 추후에 필요하면 사용하기도 했다.

(mysql은 비정상적인 접속 - tcp 연결만 하고 끊는 접속을 자주 하면 해당 아이피를 블럭킹 시키기 때문에 mysql 서버에 이 기능을 사용하려면 제한을 풀거나 다른 방법으로 mysql 서버가 살아있는지 판단해야 한다.)

컨테이너 내에 환경변수를 대입할 수도 있다. (추후에 필요하면 사용)

복수의 컨테이너를 pod 내에 넣을 수도 있다. (추후에 필요하면 사용)

```yaml
apiVersion: v1
kind: Pod # 생성하고자 하는 자원의 종류 (pod 이외에도 레플리카셋 서비스 등이 올수있음)
metadata: # 생성하고자 하는 자원의 메타데이터 (이름, 라벨 등)
  name: mysql # 이름
  labels:
    app: mysql
spec: # 상세한 자원 명세
  containers:
    - name: app
      image: ft_services/mysql:0.1
      livenessProbe:
        tcpSocket: # TCP 연결을 진행해보아 해당 포트가 살아있는지 확인한다.
          port: 3306
        initialDelaySeconds: 5 # 5초동안 기다렸다가 검사함
        timeoutSeconds: 2 # Default 1
        periodSeconds: 5 # Defaults 10
        failureThreshold: 1 # Defaults 3
```

### 레플리카셋

동일한 pod을 여러개 생성하는 것을 레플리카셋이라고 한다.

운영의 안정성을 위해 보통 pod을 여러개 운용하다 pod 중 하나가 사용 불능이 될 때 다른 pod을 운용시키는 식으로 대비한다. 이런 것을 편하게 해주는 것이 레플리카셋이다.

```yaml
apiVersion: apps/v1
kind: ReplicaSet # 생성하고자 하는 자원의 종류 (레플리카셋)
metadata:
  name: mysqlp
spec:
  replicas: 3 # 복제본 개수
  selector: # 레플리카셋으로 구분되는 조건
    matchLabels:
      app: mysql # 라벨1
      tier: app # 라벨2
  template: # 생성할 pod들의 템플릿
    metadata:
      labels:
        app: mysql
        tier: app
    spec:
      containers:
        - name: mysql
          image: ft_services/mysql:0.1
```

```shell
$> kubectl apply -f mysql_rs.yml
replicaset.apps/mysqlp created
$> kubectl get replicaset
NAME     DESIRED   CURRENT   READY   AGE
mysqlp   3         3         3       68s
$> kubectl get pod
NAME           READY   STATUS    RESTARTS   AGE
mysqlp-lvwvp   1/1     Running   0          70s
mysqlp-prrwd   1/1     Running   0          70s
mysqlp-znp7q   1/1     Running   0          70s
$> kubectl delete pod mysqlp-lvwvp
pod "mysqlp-lvwvp" deleted
$> kubectl get pod
NAME           READY   STATUS    RESTARTS   AGE
mysqlp-5g4qp   1/1     Running   0          2m17s
mysqlp-prrwd   1/1     Running   0          11m
mysqlp-znp7q   1/1     Running   0          11m
$> kubectl delete -f mysql_rs.yml
replicaset.apps "mysqlp" deleted
```

레플리카셋 명세서에 따라 레플리카셋을 생성하고 레플리카셋에 속한 pod을 삭제 했을 때 정상적으로 자동 생성되는 것을 확인하는 과정이다.

### 디플로이먼트

deployment는 replicaset에 기능이 추가된 것이라고 생각하면 편하다. 선언된 컨테이너들의 업데이트, 롤백을 지원하고 이력을 관리한다.

실제로 컨테이너를 사용할 때 pod, 레플리카셋을 직접 선언하는 것 보다는 디플로이먼트를 선언해서 사용한다.

디플로이먼트는 레플리카셋과 선언하는 형태가 유사하다. (개념적으로는 레플리카셋이 디플로이먼트에 포함된다고 볼 수 있다.)

```yaml
apiVersion: apps/v1
kind: Deployment # 생성하고자 하는 자원의 종류 (디플로이먼트)
metadata:
  name: mysqlp
spec:
  replicas: 3 # 복제본 개수
  selector: # 레플리카셋으로 구분되는 조건
    matchLabels:
      app: mysql # 라벨1
      tier: app # 라벨2
  template: # 생성할 pod들의 템플릿
    metadata:
      labels:
        app: mysql
        tier: app
    spec:
      containers:
        - name: mysql
          image: ft_services/mysql:0.1
```

```shell
$> kubectl apply -f mysql_deploy.yml
deployment.apps/mysqlp created
$> kubectl get deployment
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
mysqlp   3/3     3            3           12s
$> kubectl get replicaset
NAME               DESIRED   CURRENT   READY   AGE
mysqlp-8ff58c8d8   3         3         3       8s
$> kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
mysqlp-8ff58c8d8-d9pfj   1/1     Running   0          9m44s
mysqlp-8ff58c8d8-snd58   1/1     Running   0          9m43s
mysqlp-8ff58c8d8-zknl2   1/1     Running   0          9m43s
$> kubectl delete pod mysqlp-8ff58c8d8-d9pfj
pod "mysqlp-8ff58c8d8-d9pfj" deleted
$> kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
mysqlp-8ff58c8d8-jr8vk   1/1     Running   0          4m21s
mysqlp-8ff58c8d8-snd58   1/1     Running   0          14m
mysqlp-8ff58c8d8-zknl2   1/1     Running   0          14m
$> kubectl delete -f mysql_deploy.yml
deployment.apps "mysqlp" deleted
```

## Service

여러 가지 이유로 pod이 외부에 직접 연결되지 않는다. 그러니까 docker를 실행시킬 때 처럼 p 옵션을 줄때 네트워크가 1대1로 연결되는 것이 아니다.

그래서 제공할 서비스 별로 "서비스"를 만들고 pod과 외부를 "서비스"로 잇는다.

### ClusterIP

지금 만들 ClusterIP가 이 도커 클러스터를 외부와 이어주는 것은 아니다. ClusterIP는 도커 클러스터 내부에서만 유효하며 다른 범위에 있는 pod가 통신하도록 해 주는 것이다.

예를 들어 MySQL과 WordPress를 서로 다른 디플로이먼트로 배포할 때 서로 통신할 수 있어야 한다. 이를 위해 ClusterIP를 만들어 일종의 DNS 서버를 구성한다.

이제 MySQL 서버를 phpmyadmin이나 wordpress 등과 연동시켜 서비스로 이어보고 이 기능을 사용해 보자.

먼저 wordpress에 대한 이미지를 만들기 위해 dockerfile부터 만든다.

```dockerfile
FROM alpine:latest

COPY wordpress.conf /tmp/init/
COPY wp-config.php /tmp/init/

RUN apk --no-cache add nginx php7-mysqli php7-fpm php7-common php7-curl php7-json php7-mbstring php7-xml php7-zip php7-gd php7-soap php7-ssh2 php7-tokenizer \
&& wget https://ko.wordpress.org/latest-ko_KR.tar.gz \
&& mkdir html \
&& mkdir /run/nginx \
&& tar -zxvf latest-ko_KR.tar.gz --strip-components=1 -C /html \
&& mv /tmp/init/wordpress.conf /etc/nginx/http.d \
&& mv /tmp/init/wp-config.php /html \
&& dos2unix /html/wp-config.php \
&& php-fpm7

EXPOSE 5050
EXPOSE 3306

ENTRYPOINT \
php-fpm7 \
&& nginx -g "daemon off;"
```

wordpress.conf와 wp-config.php 은 알맞게 설정한다.

이제 두 디플로이먼트를 먼저 이어보자.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-pod # 이 이름으로 다른 컨테이너 (파드)가 하기에 명세하는 컨테이너로 접속 가능
spec:
  ports:
    - port: 3306
      protocol: TCP
  selector: # 이 설정을 가진 파드에 접근할 수 있다.
    app: mysql
    tier: app
```

이 서비스를 등록하게 되면 mysql이란 이름의 pod의 3306 포트를 mysql-pod:3306 으로 접근할 수 있도록 해준다. (mysql-pod을 노드 내부에서만 사용할 수 있는 도메인으로 만들어 주는 것이다.)

위에 wordpress.conf을 수정해야 하므로 버전을 올려 이미지를 다시 빌드하자.

빌드한 후 이미지의 버전을 올린 yml을 그대로 적용한다. (디플로이먼트의 장점인 업데이트 기능 활용)

### NodePort

위 클러스터아이피의 DNS나 아이피는 이름 그대로 클러스터 내에서만 쓸 수 있다.

클러스터 내부를 클러스터 외부(노드)에서 접근 가능하도록 NodePort 서비스를 만들어 본다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-wordpress # 노드포트 서비스 이름
spec:
  type: NodePort # 타입을 노드포트로 정의
  ports:
    - port: 5050 # 클러스터 내부의 포트
      protocol: TCP
      nodePort: 31000 # 클러스터 외부의 포트 (30000-32768 까지 할당 가능)
  selector: # 연결할 대상
    app: ft_services
    tier: wordpress
```

이제 클러스터 외부 (현재 상황에서 클러스터는 로컬 pc 내 가상 환경으로 구동되고 있다.)에서 이 클러스터의 ip를 알아낸다.

```bash
$> minikube ip
192.168.64.6
```

클러스터의 아이피는 192.168.64.6 이므로 192.168.64.6:31000으로 접근해 본다. 정상적으로 wordpress 설정 창이 뜨면 성공한 것이다.

참고로 clusterip의 기능도 겸용하기 때문에 같은 포트를 대상으로 clusterip를 만들 필요 없다.

### LoadBalancer

위 NodePort는 실제로 여러 단점이 있다. (포트 대역이 제한적이며 가리키는 노드의 IP가 바뀌면 그때마다 반영해야 한다고 한다.)

그래서 NodePort는 디버깅, 임시 용으로 사용하고 실제로 서비스를 외부에 노출시킬 때에는 LoadBalancer를 사용한다.

LoadBalancer는 알아서 가리키는 노드의 포트로 접근하게 해준다. 근데 로드밸런서는 쿠버네티스의 자체 기능이 아니라 쿠버네티스 외부에서 동작하며 외부에서 오는 요청을 쿠버네티스로 전달하게 해주는 역할을 한다. 실제로는 클라우스 서비스에 로드밸런서가 내장되어 있어 아래 사양을 명시하면 알아서 아이피를 자동으로 할당해 준다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nodeport-wordpress # 노드포트 서비스 이름
spec:
  type: LoadBalancer # 타입을 노드포트로 정의
  ports:
    - targetPort: 5050 # 클러스터 내부의 포트
      protocol: TCP
      port: 31000 # 클러스터 외부의 포트 (30000-32768 까지 할당 가능)
  selector: # 연결할 대상
    app: ft_services
    tier: wordpress
```

그래서 위 사양을 명시하면 외부 아이피를 할당해주는 로드밸런서가 없기에 pending 상태가 된다.

다행이 로드밸런서 구현체를 따로 내려받아서 사용할 수 있다. ft_service에서 사용하라고 권고하는 로드밸런서이자 실제로 테스트 혹은 소규모에서 많이 사용하는 구현체는 MetalLB이다.

공식 홈페이지 [https://metallb.universe.tf](https://metallb.universe.tf) 를 참조하면 표준 라우팅 프로토콜을 이용한 베어메탈 쿠버네티스 클러스터용 로드밸런서라고 한다. (그래서 이름이 Bare Metal Load Balancer -> MetalLB 이다.)

> 컴퓨터공학에서 Bare Metal의 본래 뜻은 소프트웨어가 설치되지 않은 하드웨어를 의미한다. 따라서 Bare Metal은 여러 가지 의미로 쓰일 수 있는데 사용자가 빈 컴퓨터 내에 OS를 직접 설치해서 사용하는 것을 베어메탈이라 할 수도 있으며 베어메탈을 가상화에 응용할 수도 있다. (기존 가상머신은 OS 레벨로 가상화를 하지만 베어메탈 가상화는 가상머신이 하드웨어(베어메탈)에 직접 접근할 수 있도록 한다)
>
> 일반적으로 쿠버네티스는 IaaS (간단하게 말하면 아마존 AWS나 구글 GCP 등) 위에서 구동되며 그런 IaaS가 로드밸런서를 별도로 제공해 준다. 하지만 현재 로컬에 설치하는 쿠버네티스에는 당연히 로드밸런서가 기본적으로 존재하지 않으므로 쿠버네티스에서 로드밸런스 사양을 명시해도 보류 상태로 유지될 수밖에 없다.
>
> 아마도 쿠버네티스를 개인 직접 설치해서 사용하면 일반적으로 IaaS같은 네트워크 환경이 구성되어 있지 않으므로 이러한 것을 도와주는 프로그램 인 것 같다.
>
> ** 틀린 내용이 있을 수 있으므로 추후 보완 예정 **

minikube에 기능을 추가로 더할 수 있다. minikube에 metallb를 추가해보자.

```bash
$> minikube addons list
|-----------------------------|----------|--------------|
|         ADDON NAME          | PROFILE  |    STATUS    |
|-----------------------------|----------|--------------|
| ambassador                  | minikube | disabled     |
| csi-hostpath-driver         | minikube | disabled     |
| dashboard                   | minikube | disabled     |
| default-storageclass        | minikube | enabled ✅   |
| efk                         | minikube | disabled     |
| freshpod                    | minikube | disabled     |
| gcp-auth                    | minikube | disabled     |
| gvisor                      | minikube | disabled     |
| helm-tiller                 | minikube | disabled     |
| ingress                     | minikube | disabled     |
| ingress-dns                 | minikube | disabled     |
| istio                       | minikube | disabled     |
| istio-provisioner           | minikube | disabled     |
| kubevirt                    | minikube | disabled     |
| logviewer                   | minikube | disabled     |
| metallb                     | minikube | disabled     |
| metrics-server              | minikube | disabled     |
| nvidia-driver-installer     | minikube | disabled     |
| nvidia-gpu-device-plugin    | minikube | disabled     |
| olm                         | minikube | disabled     |
| pod-security-policy         | minikube | disabled     |
| registry                    | minikube | disabled     |
| registry-aliases            | minikube | disabled     |
| registry-creds              | minikube | disabled     |
| storage-provisioner         | minikube | enabled ✅   |
| storage-provisioner-gluster | minikube | disabled     |
| volumesnapshots             | minikube | disabled     |
|-----------------------------|----------|--------------|
```

원래 별도로 설치를 해야 하는데 기본으로 내장되 있으므로 그냥 활성화만 하면 된다.

```bash
$> minikube addons enable metallb
🌟  'metallb' 애드온이 활성화되었습니다
```

활성화한 이후 쿠버네티스 아이피를 알아낸 다음 MetalLB가 할당할 수 있는 아이피 영역을 지정해줘야 한다.

이는 yaml 파일로 명세한다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system # MetalLB
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses: # CIDR 혹은 아이피 대역으로 표시함.
      - 192.168.64.6-192.168.64.6 # minikube ip
```

## yaml 파일로 클러스터 생성해 놓기

이제 쿠버네티스에서 클러스터 생성을 위한 기본적인 지식을 습득했으므로 앞으로는 클러스터를 생성한 후 도커 이미지의 버전을 올려 가면서 롤아웃/롤백을 수행하며 과제를 완성해 나갈 것이다.

일단 쿠버네티스 클러스터의 기본적인 구조를 다시 적어보자.

- 디플로이먼트
  - WordPress : wordpress 동작
  - Nginx : https 웹서버, ssh 및 프록시, 리다이렉트
  - PhpMyAdmin : phpmyadmin 동작
  - FTPS : 파일 서버
  - MySQL : DB
  - Grafana : 수집한 데이터 통계
  - InfluxDB : 실행되는 노드들의 데이터 수집

- 로드밸런서
  - grafana : 3000
  - wordpress : 5050
  - nginx : 80/443/22
  - phpmyadmin : 5000
  - ftps : 21 (외에 포트 하나 더 필요함)
- 클러스터 IP
  - nginx -> phpmyadmin : 5000
  - 다른 노드들 -> InFluxDB : 8086
  - phpmyadmin, wordpress -> MySQL : 3306

위 구조를 바탕으로 디플로이먼트, 로드밸런서와 클러스터아이피 명세를 작성해 보자.

### Deployment

#### WordPress

```yaml
apiVersion: apps/v1
kind: Deployment # 생성하고자 하는 자원의 종류 (디플로이먼트)
metadata:
  name: wordpress # Deployment Name
spec:
  replicas: 1 # pod 복제본 개수
  selector: # 구분되는 조건
    matchLabels:
      app: ft_services # 라벨1 (앱 이름)
      tier: wordpress # 라벨2
  template: # 생성할 pod들의 템플릿
    metadata:
      labels:
        app: ft_services # 라벨1 (앱 이름)
        tier: wordpress # 라벨2
    spec:
      containers:
        - name: wordpress # 컨테이너 이름
          image: ft_services/wordpress:0.1
```

#### Nginx ~ InfluxDB

위 양식을 기반으로 라벨, 이름, 이미지만 바꾼다.

### 클러스터 IP

#### phpmyadmin

```yaml
apiVersion: v1
kind: Service
metadata:
  name: phpmyadmin-ip
spec:
  ports:
    - port: 5000
      protocol: TCP
  selector: # 이 설정을 가진 파드에 접근할 수 있다.
    app: ft_services
    tier: phpmyadmin
```

#### InfluxDB

```yaml
apiVersion: v1
kind: Service
metadata:
  name: influxdb-ip
spec:
  ports:
    - port: 8086
      protocol: TCP
  selector: # 이 설정을 가진 파드에 접근할 수 있다.
    app: ft_services
    tier: influxdb
```

#### MySQL

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-ip
spec:
  ports:
    - port: 3306
      protocol: TCP
  selector: # 이 설정을 가진 파드에 접근할 수 있다.
    app: ft_services
    tier: mysql
```

### LoadBalancer

#### Grafana

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadbalancer-grafana
spec:
  type: LoadBalancer
  ports:
    - port: 3000
      protocol: TCP
      nodePort: 33000
  selector:
    app: ft_services
    tier: wordpress
```

#### Wordpress ~ FTPS

적절히 위 내용을 기반으로 바꾼다.

완료하면 다음과 같은 환경이 될것이다.

```bash
$> kubectl get deployment
NAME         READY   UP-TO-DATE   AVAILABLE   AGE
ftps         1/1     1            1           9h
grafana      1/1     1            1           149m
influxdb     1/1     1            1           144m
mysql        1/1     1            1           3h2m
nginx        1/1     1            1           10h
phpmyadmin   1/1     1            1           10h
wordpress    1/1     1            1           21h
$> kubectl get services
NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                                   AGE
influxdb-ip               ClusterIP      10.99.66.176     <none>         8086/TCP                                  18s
kubernetes                ClusterIP      10.96.0.1        <none>         443/TCP                                   24h
loadbalancer-ftps         LoadBalancer   10.110.115.37    192.168.64.7   21:31292/TCP,42420:30282/TCP              98m
loadbalancer-grafana      LoadBalancer   10.110.135.139   <pending>      3000:32614/TCP                            98m
loadbalancer-nginx        LoadBalancer   10.106.156.226   <pending>      80:31572/TCP,443:30505/TCP,22:31255/TCP   98m
loadbalancer-phpmyadmin   LoadBalancer   10.111.241.186   <pending>      5000:30570/TCP                            98m
loadbalancer-wordpress    LoadBalancer   10.98.156.141    <pending>      5050:30406/TCP                            98m
mysql-ip                  ClusterIP      10.102.97.225    <none>         3306/TCP                                  18s
phpmyadmin-ip             ClusterIP      10.111.183.158   <none>         5000/TCP                                  18s
$> 
```

위처럼 뜨는 환경에서 환경을 맞춰나갈 것이다.

### 한 로드밸런서 외에 다른 로드밸런서에 아이피가 할당이 안되는 문제

[https://metallb.universe.tf/usage/](https://metallb.universe.tf/usage/) 의 하단을 참조하면 "Services do not share IP addresses." 라고 되어 있다. 아마도 로드밸런서를 할당할 때 마다 MetalLB 설정에 명시된 범위 내에서 아이피를 할당하는데 현재 설정에서는 아이피 범위가 단 하나라 나머지 로드밸런서가 할당이 되지 않는 것으로 보인다.

여러 로드밸런서가 같은 아이피를 공유하게 하려면 서비스를 선언할 때  `metallb.universe.tf/allow-shared-ip` 어노테이션을 추가해야 한다고 한다.

기존 로드밸런서를 삭제한 후 로드밸런서 yaml 파일에 metadata에 다음 구문을 삽입한다.

```yaml
metadata:
  annotations:
    metallb.universe.tf/allow-shared-ip: shared
```

다시 적용해본다.

```bash
$> kubectl get services
NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)                                   AGE
influxdb-ip               ClusterIP      10.99.66.176     <none>         8086/TCP                                  12m
kubernetes                ClusterIP      10.96.0.1        <none>         443/TCP                                   24h
loadbalancer-ftps         LoadBalancer   10.99.99.39      192.168.64.7   21:31944/TCP,42420:31317/TCP              4s
loadbalancer-grafana      LoadBalancer   10.108.126.108   192.168.64.7   3000:32145/TCP                            4s
loadbalancer-nginx        LoadBalancer   10.110.119.144   192.168.64.7   80:32284/TCP,443:31625/TCP,22:31430/TCP   4s
loadbalancer-phpmyadmin   LoadBalancer   10.109.37.175    192.168.64.7   5000:32099/TCP                            3s
loadbalancer-wordpress    LoadBalancer   10.101.71.77     192.168.64.7   5050:31458/TCP                            3s
mysql-ip                  ClusterIP      10.102.97.225    <none>         3306/TCP                                  12m
phpmyadmin-ip             ClusterIP      10.111.183.158   <none>         5000/TCP                                  12m
$> 
```

이제 아이피가 정상적으로 할당된다.

### FTPS 동작 확인

FileZilla로 연결 테스트를 해 보면 로그인은 되지만 데이터 연결이 되지 않았다.

이 문제를 수정해 보기 위해 현재 실행중인 pod의 컨테이너에 접속해 보겠다.

```bash
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-6d946fdd7f-75zxg         1/1     Running   2          22h
grafana-6dbc757b84-j55n2      1/1     Running   0          15h
influxdb-85b6c8bcdb-qsjtn     1/1     Running   0          15h
mysql-6598db6b58-zf5p5        1/1     Running   0          16h
nginx-bdb46589b-7mv6z         1/1     Running   0          23h
phpmyadmin-7c7c6bdfc7-z2h6k   1/1     Running   0          22h
wordpress-57894bcd45-9r5mj    1/1     Running   0          34h
$> kubectl exec -it ftps-6d946fdd7f-75zxg -- sh
/ #
```

현재 상태로 vsftpd 설정파일을 수정하고 데몬을 재시작하고 싶어도 할 수 없다. (kill 명령어로 강제 종료가 되지 않는다.) 따라서 해당 컨테이너를 실행할 때 vsftpd를 실행하지 못하게 막아놓고 쉘에 접속해서 테스트해야 한다. (dockerfile에 CMD로 정의된 명령은 해당 이미지에 다른 명령을 실행하면 그 명령어가 실행된다.)

ftps deploy yaml 파일에 다음 구문을 삽입한 후 다시 deploy를 적용해 vsftpd 데몬 실행을 막는다.

```yaml
        - name: ftps # 컨테이너 이름
          image: ft_services/ftps:0.1
          command: ["sleep"]
          args: ["inf"]
```

다시 쉘에 접속한다.

```bash
$> kubectl apply -f ftps/ftps_deploy.yml
deployment.apps/ftps configured
$> kubectl get pods
NAME                          READY   STATUS        RESTARTS   AGE
ftps-75f885d4c4-2ljjk         1/1     Terminating   2          47m
ftps-7b5447bd7f-sm58f         1/1     Running       0          13s
grafana-6dbc757b84-j55n2      1/1     Running       0          18h
influxdb-85b6c8bcdb-qsjtn     1/1     Running       0          18h
mysql-6598db6b58-zf5p5        1/1     Running       0          18h
nginx-bdb46589b-7mv6z         1/1     Running       0          26h
phpmyadmin-7c7c6bdfc7-z2h6k   1/1     Running       0          25h
wordpress-57894bcd45-9r5mj    1/1     Running       0          37h
$> kubectl exec -it ftps-7b5447bd7f-sm58f -- sh
/ # ps
PID   USER     TIME  COMMAND
    1 root      0:00 sleep inf
    9 root      0:00 sh
   15 root      0:00 ps
/ #
```

이제 해당 환경에서 vsftpd.conf 파일을 변경해가며 디버깅한다.

다시 FileZilla 로그를 확인해보자. 오류를 자세히 보면 패시브 모드에서 127.0.0.1 아이피로 접속 시도를 해서 발생하였다.

이전에 설정한 vsftpd.conf 파일을 다시 보았다.

pasv_address=127.0.0.1 이라고 변수 설정이 되었는데 저것을 특정하게 되면 패시브 모드에서 저 아이피로 접근하게 되어 연결에 실패하게 된다.

해결책은 저 주소에 현재 동작중인 클러스터 IP의 공인 IP를 삽입해야 한다. minikube ip 명령어로 쿠버네티스 클러스터의 아이피를 알아내서 저 아이피에 삽입한 이후 재시도를 해본다.

```bash
$> minikube ip
192.168.64.9
$> kubectl exec -it ftps-7b5447bd7f-sm58f -- sh
/ # vi vsftpd.conf
/ # vsftpd vsftpd.conf
```

다시 FileZilla로 접속해보면 접속이 잘 되는것을 확인할 수 있다. 아무래도 쉘 스크립트를 이용해 vsftpd.conf 파일을 minikube ip에 따라 수정할 수 있는 스크립트를 만들어야 될 듯 하다.

관련 내용을 구글링해서 찾아보면 sed 라는 명령어를 이용해서 구현하는 듯 하다.

기본적으로는  ` sed -i "s/[수정할 문자열]/[대치할 문자열]/" [file]` 와 같이 사용한다. 하지만 현재 사용 환경인 mac os에서는 i 옵션을 지원하지 않고 변경된 파일 내용이 그대로 표준출력으로 출력된다. 따라서 ` sed "s/[수정할 문자열]/[대치할 문자열]/" [file] > [file]`  와 같이 사용해야 한다.

ftp 설정파일의 vsftpd.conf 파일 내에 pasv_address=CLUSTER_IP_ADDR로 변경하고 도커 이미지 빌드 전 `sed "s/CLUSTER_IP_ADDR/$(minikube ip)/" vsftpd.conf > vsftpd.conf` 명령으로 아이피 셋을 한다.

근데 위처럼 하면 동일한 파일에 대해 동시에 읽고 수정하기 때문에 파일 내용이 통째로 날라가기 때문에 임시 파일을 생성해야 한다.

```bash
$> sed "s/CLUSTER_IP_ADDR/$(minikube ip)/" vsftpd.conf > vsftpd.conf.tmp
$> mv vsftpd.conf.tmp vsftpd.conf
```

근데 다시 메뉴얼을 자세히 보니 맥에서도 임시 파일을 생성하지 않고 변경하는 방법이 있었다.

```
Replace all occurances of `foo' with `bar' in the file test.txt, without creating a backup of the file:

sed -i '' -e 's/foo/bar/g' test.txt
```

그래서 다시 바꿨다.

```bash
$> sed -i '' -e "s/CLUSTER_IP_ADDR/$(minikube ip)/g" vsftpd.conf
```

추가로 metallib yaml도 동일하게 적용한다.

### phpmyadmin 동작 확인

phpmyadmin은 phpmyadmin과 mysql 두가지 모두 동작 확인을 해야 한다.

일단 phpmyadmin에 접속해보자.

현재 상태에서는 사이트 접속은 되지만 mysql 접속에는 실패한다. phpmyadmin 설정을 다시 보자.

현재 설정 파일을 보면 mysql 서버 주소가 localhost로 되어 있다. phpmyadmin pod에 접근해서 쉘로 접속해서 설정을 바꿔보자.

현재 설정된 clusterip 설정을 참조하면 mysql은 mysql-ip라는 도메인으로 접근할 수 있다. localhost를 mysql-ip로 수정하고 다시 시도해 보자.

```bash
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-7b5447bd7f-84628         1/1     Running   0          158m
grafana-6dbc757b84-2v6vk      1/1     Running   0          158m
influxdb-85b6c8bcdb-tkhfl     1/1     Running   0          158m
mysql-6598db6b58-l4ftc        1/1     Running   0          158m
nginx-bdb46589b-7c6bp         1/1     Running   0          158m
phpmyadmin-7c7c6bdfc7-7rh6k   1/1     Running   0          158m
wordpress-57894bcd45-mqxd8    1/1     Running   0          158m
$> kubectl exec -it phpmyadmin-7c7c6bdfc7-7rh6k -- sh
/ # vi html/config.inc.php
```

수정하니 접속은 잘 된다. 하지만 사이트 밑에 tmp 경로에 접근할 수 없다는 경고가 뜬다. (ft_server와 같은 이슈)

ps -ef로 php-fpm의 uid를 알아해서 chown 명령어를 이용해 소유자를 바꾼다. (혹은 사이트에서 나오는대로 tmp 폴더를 만들어 그 폴더에 대해서만 chmod 777 처리해도 된다.)

위 변경사항들을 dockerfile에 반영하고 버전업 후 다시 빌드해서 적용한다.

### Wordpress 테스트

포트 5050으로 접속한다. 데이터베이스 연결 구축 오류라고 뜬다. 설정파일을 수정해보자.

```bash
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-7b5447bd7f-84628         1/1     Running   0          158m
grafana-6dbc757b84-2v6vk      1/1     Running   0          158m
influxdb-85b6c8bcdb-tkhfl     1/1     Running   0          158m
mysql-6598db6b58-l4ftc        1/1     Running   0          158m
nginx-bdb46589b-7c6bp         1/1     Running   0          158m
phpmyadmin-7c7c6bdfc7-7rh6k   1/1     Running   0          158m
wordpress-57894bcd45-mqxd8    1/1     Running   0          158m
$> kubectl exec -it wordpress-57894bcd45-mqxd8 -- sh
/ # vi html/wp-config.php
```

db 아이피를 수정하면 정상적으로 설치 화면이 뜬다. 설치 후 접속해보자.

요구사항에 따르면 관리자를 포함한 유저들이 이미 생성되어 있어야 한다고 한다.

wordpress 웹에서 유저들을 적당히 생성한 후 phpmyadmin에서 wordpress databsse를 선택한 후 내보내기 탭에서 데이터베이스를 내보낸다. 해당 파일은 sql 쿼리문으로 이루어져 있다.

내보낼 때 `CREATE DATABASE / USE` 구문 추가 를 체크해야 해당 데이터베이스에 데이터를 삽입할 수 있다.

이제 mysql dockerfile에 해당 sql 파일을 작성하는 구문을 추가한다. 이후 mysql 이미지 버전을 올린 후 다시 배포해본다.

하지만 "ERROR: 1105  Boostrap file error. Query size exceeded 20000 bytes near ''." 라는 오류가 빌드할 때 발생한다. wordpress 쿼리 크기가 너무 커서 mysql 데몬으로 삽입이 안되는것 같다. 클라이언트를 설치해 클라이언트로 삽입하는 방식으로 변경하였다.

그런데 dockerfile 내에 RUN 구문에서 mysql 데몬을 실행시킨 상태에서 mysql 클라이언트를 실행시키면 동작이 중지되므로 wordpress database를 삽입시키는 구문은 쉘 스크립트로 별도로 분리하였다.

위 변경사항들을 dockerfile에 반영하고 버전업 후 다시 빌드해서 적용하면 정상적으로 사용자 데이터가 삽입되어 있다.

### nginx 테스트

먼저 ssh 접속을 테스트해보자.

```bash
$> ssh root@192.168.64.9
The authenticity of host '192.168.64.9 (192.168.64.9)' can't be established.
RSA key fingerprint is SHA256:+M0zWj6YDwpAqOOW3wfsjp/2j9J61vbLvGrLYbqUwuw.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.64.9' (RSA) to the list of known hosts.
root@192.168.64.9's password:
Welcome to Alpine!

The Alpine Wiki contains a large amount of how-to guides and general
information about administrating Alpine systems.
See <http://wiki.alpinelinux.org/>.

You can setup the system with the command: setup-alpine

You may change this message by editing /etc/motd.

nginx-bdb46589b-7c6bp:~#
```

접속이 잘 된다.

이제 http로 접속해서 https로 리다이렉트 되는지 확인한다. -> 잘된다.

하지만 wordpress/phpmyadmin 접근은 되지 않는다. 아마 아이피 설정이 안된듯하다.

wordpress는 위에 클러스터 아이피를 삽입하는 것으로 해결했지만 phpmyadmin은 작동되지 않아(404 오류) 구글에 "nginx reverse proxy returns 404" 와 같이 검색하니 [https://stackoverflow.com/questions/16157893/nginx-proxy-pass-404-error-dont-understand-why](https://stackoverflow.com/questions/16157893/nginx-proxy-pass-404-error-dont-understand-why) 와 같은 방법으로 해결했다.

하지만 접속은 되지만 phpmyadmin에 "There is a mismatch between HTTPS indicated on the server and client. This can lead to a non working phpMyAdmin or a security risk. Please fix your server configuration to indicate HTTPS properly." 라는 오류가 뜬다.

대충 번역해보면 서버와 클라이언트 간 HTTPS 정보가 일치하지 않다고 하는데 지금 현재 TLS 서버는 nginx에서 동작하고 phpmyadmin은 백앤드 서버에서 따로 동작한다. 프록시로 nginx에서 phpmyadmin이 동작하는것처럼 보이기 때문에 문제가 된다고 한다.

오류 메세지를 적당히 긁어 구글링해 해결책을 찾는다.

이후로 로그인 하면 403 not found 오류가 뜬다. phpmyadmin은 내부에서 phpmyadmin 경로를 직접 찾아서 정하는데 그 주소는 nginx pod - phpmyadmin pod 로 연결되는 주소로 정해지기 때문에 외부에서 nginx로 요청되는 주소랑 동일하게 맞춰야 한다.

따라서 nginx conf를 먼저 다음과 같이 설정한다.

```
    location /phpmyadmin/ {
        proxy_set_header X-Forwarded-Proto https;
        proxy_pass http://phpmyadmin-ip:5000/phpmyadmin/;
    }
```

다음 phpmyadmin pod에서 /phpmyadmin/ 으로 접근해도 phpmyadmin이 접속되도록 설정하면 된다.

### MySQL 설정

MySQL 같은 어플리케이션은 상태 유지가 필요하다. (Nginx 처럼 언제 꺼졌다 켜저도 상관 없는 어플리케이션은 상관없다.)이런 어플리케이션들은 스테이트풀셋이라는 것을 이용해서 관리한다.

관련 문서 : [https://kubernetes.io/ko/docs/concepts/workloads/controllers/statefulset/](https://kubernetes.io/ko/docs/concepts/workloads/controllers/statefulset/)

## 추가로 알아야 할 것들

### ConfigMap

키-값 쌍으로 데이터를 저장하는 객체이다. 이것도 yaml 형식으로 지정하며 키는 파일명, 환경변수 등이 될 수 있으며 값은 쉘코드 등이 될 수 있다.

기본 양식은 다음과 같다.

```yaml
apiVersion: v1
kind: ConfigMap # ConfigMap
metadata:
  name: configmap-example # ConfigMap
data:
  username: "pujuhong" # 키 : 값 대응
  config.conf: | # 파일처럼 쓰는 키 :
    #!/bin/sh
    mysqld_safe --user=root --datadir=/var/lib/mysql &
    sleep 5
    mysql -u root < /tmp/init/wp_db.sql
    sleep inf
```

### Volume

컨테이너는 파일이 격리되어 있으며 컨테이너가 손상되면 파일도 손상된다. 혹은 컨테이너가 지워지면 파일도 지워진다.

혹은 같은 pod 내에 같은 파일을 공유할 필요가 있을 수 있다.

쿠버네티스의 Volume으로 특정한 공간을 만들어 컨테이너의 상태에 구애받지 않는 공간을 만들 수 있다.

Volume의 종류는 다양하며 클라우드 서비스와 연관되어 있는 볼륨도 있다. (보통 클라우드 사이트에 따라 간다.)

### Persistent Volume

퍼시스턴트 볼륨은 쿠버네티스 클러스터 내의 볼륨이다.

### 요구사항 다시 확인

원래대로라면 쿠버네티스 클러스터에서 데이터베이스를 관리할 때에는 statefulset이라는 것을 사용해서 배포해야 한다. 하지만 해당 방법을 사용하면 일단 설정이 복잡하며 요구사항에서 동일한 데이터베이스를 여러개 띄우라는 말도 없다. 따라서 데이터 영속성만 적용하면 되는데 이는 위에서 퍼시스턴트 볼륨만 사용하면 되는 것으로 이해하였다.

요구사항에 따르면 `In case of a crash or stop of one of the two database containers, you will have to make shure the data persist.` 라고 명시되어 있는데 저 two database 컨테이너는 각각의 데이터베이스 (그러니까 InfluxDB, MySQL) 가 멈추거나 종료될 때 내부 데이터가 유지되는지를 확인하란 말이다. 따라서 Statefulset까지 공부할 필요는 없는 것으로 확인했다. (하지만 추후에 독학으로 추가로 공부해 볼 예정이다. 실무에서는 저 방법을 사용할 것이므로...)

MySQL의 영속성을 유지하려면 관련 파일을 유지하면 되는데  MySQL을 실행할 때 옵션을 다시 보자. `mysqld_safe --user=root --datadir=/var/lib/mysql` 인데 /var/lib/mysql 경로에 모든 설정파일이 들어간다. 그래서 저 경로를 pod에 상태와 무관하게 계속 유지를 시키면 되는것이다.

그러한 디렉토리를 만드려면 퍼시스턴스 볼륨을 만들면 된다. 퍼시스턴스 볼륨은 퍼시스턴스 볼륨 클레임이라는 것을 이용해서 만든다.

아래 명시는 퍼시스턴스 볼륨을 만드는 일종의 요청이다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

mysql-data 이라는 버시스턴스 볼륨을 만든다. 이를 MySQL yaml 내에 추가한다.

```yaml
apiVersion: apps/v1
kind: Deployment # 생성하고자 하는 자원의 종류 (디플로이먼트)
metadata:
  name: mysql # Deployment Name
spec:
  replicas: 1 # pod 복제본 개수
  selector: # 구분되는 조건
    matchLabels:
      app: ft_services # 라벨1 (앱 이름)
      tier: mysql # 라벨2
  template: # 생성할 pod들의 템플릿
    metadata:
      labels:
        app: ft_services # 라벨1 (앱 이름)
        tier: mysql # 라벨2
    spec:
      containers:
        - name: mysql # 컨테이너 이름
          image: ft_services/mysql:0.3
          volumeMounts: # 퍼시스턴스 볼륨 접근
          - name: mysql-data-v
            mountPath: /var/lib/mysql # 경로
      volumes: # 컨테이너 이름
        - name: mysql-data-v # 볼륨 이름
          persistentVolumeClaim:
            claimName: mysql-data # 클레임 네임
```

이렇게 지정하면 /var/lib/mysql 폴더는 mysql pod이 종료되도 저 폴더 내용은 유지된다.

이제 pod를 실행할 때 init 되는 Docker / init.sh를 수정해보자.

쿼리문 실행은 저 경로 내에 아무 파일이 없을 때에만 실행해야 하므로 쿼리문 삽입 구문을 init.sh로 몰아 넣으며 쉘 파일 내용은 다음과 같이 작성한다.

```bash
#!/bin/sh
if [ -d "/var/lib/mysql/mysql" ]; then
    mysqld_safe --user=root --datadir=/var/lib/mysql
else
    mysql_install_db --user=root --datadir=/var/lib/mysql
    mysqld_safe --user=root --datadir=/var/lib/mysql &
    sleep 5
    mysql -u root < /tmp/init/query.sql
    mysql -u root < /tmp/init/wp_db.sql
    sleep inf
fi
```

다음과 같이 작성하면 MySQL Pod 첫 실행때마 init 작업을 실행하며 오류로 pod이 종료되 재실행하거나 할때는 init을 하지 않는다.

wordpress를 실행한 다음 아무 글이나 작성해 보고 mysql pod를 강제종료 해보자. (강제종료 하면 쿠버네티스가 알아서 다시 실행한다.)

```bash
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-6d946fdd7f-fch4q         1/1     Running   0          56m
grafana-6dbc757b84-lhtwj      1/1     Running   0          56m
influxdb-85b6c8bcdb-grmzm     1/1     Running   0          56m
mysql-5867bf4887-s7z75        1/1     Running   0          56m
nginx-647595897-ll746         1/1     Running   0          56m
phpmyadmin-7f8968cdf5-g952r   1/1     Running   0          56m
wordpress-57894bcd45-9q6mn    1/1     Running   0          56m
$> kubectl delete pod mysql-5867bf4887-s7z75
pod "mysql-5867bf4887-s7z75" deleted
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-6d946fdd7f-fch4q         1/1     Running   0          57m
grafana-6dbc757b84-lhtwj      1/1     Running   0          57m
influxdb-85b6c8bcdb-grmzm     1/1     Running   0          57m
mysql-5867bf4887-22vqt        1/1     Running   0          44s
nginx-647595897-ll746         1/1     Running   0          57m
phpmyadmin-7f8968cdf5-g952r   1/1     Running   0          57m
wordpress-57894bcd45-9q6mn    1/1     Running   0          57m
$> 
```

재실행 해 보고 이전에 작성한 글이 남아 있는지 확인해 보자. 정상적으로 작동된다면 남아있을 것이다.

해당 기능구현을 하며 wordpress 버그도 수정하였다. minikube ip가 바뀌면 wordpress가 정상 동작을 하지 않는 문제였는데

이 문제는 wordpress를 백업한 sql 내의 ip를 현재의 minikube ip로 바꾸는 작업을 수행하면 된다.

예시는 다음과 같다. (setup.sh)

```bash
#!/bin/bash
IP=$(minikube ip)
sed -i '' -e "s/CLUSTER_IP_ADDR/$IP/g" srcs/mysql/files/wp_db.sql
# kubectl apply ...
```

### InfluxDB

일단 InfluxDB로 다른 pod들의 데이터를 수집하는 부분을 구현해야 한다.

수집은 telegraf 라는 프로그램을 이용해서 한다.

위에서 telegraf로 config 파일을 수정해보며 주석같은 부분을 제거하며 필요한 부분을 수정해보자.

```
[agent]
  ## 데이터 간격 수집 인터벌
  interval = "10s"
  ## 수집하는 시간 단위를 인터벌에 맞추는가
  round_interval = true

# db data
[[outputs.influxdb]]
  ## InfluxDB URI
  urls = ["http://influxdb-ip:8086"]
  ## db name
  database = "telegraf-beta1"

# Read metrics about cpu usage
[[inputs.cpu]]
  ## cpu 사용량 수집
  percpu = true
  ## Whether to report total system cpu stats or not
  totalcpu = true
  ## If true, collect raw CPU time metrics.
  collect_cpu_time = false
  ## If true, compute and report the sum of all non-idle CPU states.
  report_active = false

# Read metrics about disk usage by mount point
[[inputs.disk]]
  ## By default stats will be gathered for all mount points.
  ## Set mount_points will restrict the stats to only the specified mount points.
  # mount_points = ["/"]

  ## Ignore mount points by filesystem type.
  ignore_fs = ["tmpfs", "devtmpfs", "devfs", "iso9660", "overlay", "aufs", "squashfs"]

# Get kernel statistics from /proc/stat
[[inputs.kernel]]
  # no configuration

# Read metrics about memory usage
[[inputs.mem]]
  # no configuration

# Read metrics about network interface usage
[[inputs.net]]
  interfaces = ["eth0"]

# Read TCP metrics such as established, time wait and sockets counts.
[[inputs.netstat]]
  # no configuration

# Read metrics about system load & uptime
[[inputs.system]]
  ## Uncomment to remove deprecated metrics.
  # fielddrop = ["uptime_format"]

# Read metrics from one or many mysql servers
[[inputs.mysql]]
  servers = ["tcp(127.0.0.1:3306)/"]
```

mysql 서버에서 telegraf 를 실행한 다음 InfluxDB 쉘에 접속해 데이터가 잘 쌓이는지 체크해본다.

다른 pod들에게도 데이터 수집 로직을 추가한다.

존재하는 모든 pod에 대해 데이터 수집이 잘 되는지 확인은 다음과 같이 한다.

```bash
$> kubectl get pods
NAME                          READY   STATUS    RESTARTS   AGE
ftps-75f885d4c4-4knkr         1/1     Running   0          36m
grafana-644575c45-qrx24       1/1     Running   0          36m
influxdb-7cdd559dc7-r5lf4     1/1     Running   0          33m
mysql-5c4c9b7b9b-wj66g        1/1     Running   0          36m
nginx-7f4577bf74-hbvt5        1/1     Running   0          36m
phpmyadmin-76d78558db-crssd   1/1     Running   0          36m
wordpress-75b6bd5576-pflrr    1/1     Running   0          36m
$> kubectl exec -it influxdb-7cdd559dc7-r5lf4 -- sh
/ # influx
Connected to http://localhost:8086 version 1.8.3
InfluxDB shell version: 1.8.3
> show databases
name: databases
name
----
influxdb
wordpress
phpmyadmin
nginx
ftps
grafana
mysql
_internal
```

다음과 같이 정상적으로 데이터베이스에 데이터가 들어가는지 확인한다.

### Grafana

Grafana에서는 수집된 데이터를 바로 볼 수 있도록 세팅해야 한다.

Grafana에는 설정 데이터 (로그인 등)가 `/usr/share/grafana/data/grafana.db` 에 저장된다고 한다. 따라서 먼저 Grafana를 설정해 두고 저 db 파일을 컨테이너 빌드 시에 카피해두면 미리 설정된 Grafana를 열 수 있다.

먼저 Grafana에서 DB를 추가한 다음 Grafana로 설정파일을 백업해 보자. pod에서 데이터를 추출하는 명령어는 다음과 같다.

```shell
$> kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
ftps-75f885d4c4-4knkr         1/1     Running   0          91m
grafana-644575c45-bz2n6       1/1     Running   0          11m
influxdb-7cdd559dc7-r5lf4     1/1     Running   0          88m
mysql-5c4c9b7b9b-wj66g        1/1     Running   0          91m
nginx-7f4577bf74-hbvt5        1/1     Running   0          91m
phpmyadmin-76d78558db-crssd   1/1     Running   0          91m
wordpress-75b6bd5576-pflrr    1/1     Running   0          91m
$> kubectl cp grafana-644575c45-bz2n6:/usr/share/grafana/data/grafana.db ./grafana.db
```

다시 pod으로 데이터를 집어 넣는 명령어는 다음과 같다.

```shell
$> kubectl cp ./grafana.db grafana-644575c45-p9tln:/usr/share/grafana/data/grafana.db
```

## 쿠버네티스 대시보드 접속

멘데토리 항목 중 대시보드를 접속할 수 있어야 한다고 한다.

minikube를 이용해서 쉽게 접속할 수 있지만 minikube를 이용하면 구동 엔진에 너무 의존적이기 때문에 kubectl을 이용한다.

```sh
$> kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

위 yaml은 대시보드를 활성화 하는 명세서이다.

명세서를 적용한 후 아래 링크로 접속하면 대시보드에 접속이 가능하다.

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

하지만 위 대시보드에 접속하기 위해선 사용자 등록 및 토큰이 필요하다. 토큰을 생성해 보자.

```sh
$> kubectl create serviceaccount dashboard-admin-sa
serviceaccount/dashboard-admin-sa created
$> kubectl create clusterrolebinding dashboard-admin-sa
--clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
Error: required flag(s) "clusterrole" not set
zsh: command not found: --clusterrole=cluster-admin
$> kubectl create clusterrolebinding dashboard-admin-sa --clusterrole=cluster-admin --serviceaccount=default:dashboard-admin-sa
clusterrolebinding.rbac.authorization.k8s.io/dashboard-admin-sa created
$> kubectl get secrets
NAME                             TYPE                                  DATA   AGE
dashboard-admin-sa-token-lqn75   kubernetes.io/service-account-token   3      26s
default-token-djwdx              kubernetes.io/service-account-token   3      7m41s
$> kubectl describe secret dashboard-admin-sa-token-lqn75
Name:         dashboard-admin-sa-token-lqn75
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: dashboard-admin-sa
              kubernetes.io/service-account.uid: dd42c0b3-ae0a-4d1c-a48c-1963077b4f80

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1111 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6Im1Sdnp3d3RMNmtNZ0RCTnJtc3J5WWJ3eEp5Y0cwdG5aLXB0VnQxTkNLSE0ifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRhc2hib2FyZC1hZG1pbi1zYS10b2tlbi1scW43NSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tc2EiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiJkZDQyYzBiMy1hZTBhLTRkMWMtYTQ4Yy0xOTYzMDc3YjRmODAiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6ZGVmYXVsdDpkYXNoYm9hcmQtYWRtaW4tc2EifQ.1UOnlI0xo3v_w0HMFfJFaqxEVV0wZRzsVSPyk-QfVxn4qOWN09O1WrAQLE0OhuVs2F1U1RtEV6wyi1Jwo1DVe8uRl4oKXYSJlPx64nvSUCsBfRHCzRC0Cjr2672dRDnouF1GLWkAQQ86TiRQ_Q3kTdxzAy9Sh9CCds3D-chx4VxWmrK0pCk9QQ_zECg3pc4lZIGgxxcT0hDhvr0GKxcqMmOmj8Swrzu4MpCBBVNWZY5NkxhA7NJfr9NqZKT3E_nWK4a0cQva1Vi31t0ErjfD7I3Y13eCNBGjoi97qSXfcFkc59goraZV_s5drFpmrPjDxK5WVgxZkLfbQHivUpDThQ
```

위 긴 토큰을 복사해서 저 uri에 접속해서 붙여넣고 접속하면 된다.

이 행위를 자동화하거나 수동으로 하거나는 아마도 선택사항인 것 같다.

### 디플로이먼트 퍼시스턴스 강건화

파드 내 컨테이너의 특정 프로세스의 동작을 체크하여 동작되지 않거나 동작을 받아들이지 않는다면 서비스를 재시작 한다.

이에 대한 설정은 세 가지가 있다.

- livenessProbe : 컨테이너 내부 프로세스가 동작 중인지 주기적으로 확인한다. 요청에 대한 응답이 없다면 컨테이너를 재시작한다.
- readinessProbe : 컨테이너 내부 프로세스가 요청을 받을 수 있는지를 확인한다. 요청을 받을 수 없다면 해당 컨테이너의 IP를 제거해 요청을 받을 수 없도록 한다.
- startupProbe : 컨테이너 내부 프로세스가 시작되었는지 확인한다. 시작되지 않았다면 재시작 한다.

여기서는 livenessProve만 사용한다.

### 어떤 컨테이너에 대해 적용하나?

원칙적으로는 당연히 위에서 검사하고자 하는 프로세스를 검사할 때 적용한다. 또 컨테이너 내부의 프로세스 혹은 서비스가 데드락 상태나 실행되고는 있지만 아무런 기능도 하지 않거나 그런 여러 상황을 감지하고 비정상적인 상황을 감지하여 컨테이너를 재시작 하고자 하기 위함이다. 하지만 본 과제에거 퍼시스턴스를 체크할 때 해당 프로세스를 강제로 종료하는 식으로 테스트 하며 도커의 컨테이너는 기본적으로 실행하는 (RUN이나 CMD로 실행되는 프로세스) 프로세스가 종료되면 컨테이너도 종료되서 그런 경우엔 쿠버네티스가 알아서 다시 실행시켜준다. 하지만 그 프로세스 외에 내부에서 백그라운드로 실행되는 프로세스는 종료된다고 쿠버네티스가 알아서 재시작 시켜주지 않으므로 그런 백그라운드 프로세스 혹은 서비스에 대해서는 적용해야 한다.

