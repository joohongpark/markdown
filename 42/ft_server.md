# ft_server 요구사항

## 목적

- Docker 컨테이너를 이용해 Wordpress, phpmyadmin, SQL Database 환경을 구성한다.
- Docker 컨테이너 사용 및 자동화를 익힌다. 또 간단한 웹 서비스를 만들어 본다.

## 요구사항

- 하나의 Docker 컨테이너에서만 구동되어야 한다.
- Wordpress, phpmyadmin, SQL Database 환경을 docker 컨테이너 내부에 셋팅한다.
- 위 서비스를 쉘 스크립트 및 dockerfile로 자동 설치/실행시킨다.
- 리포 루트 디렉토리에 Dockerfile을 두고, 필요한 파일(WordPress도)들은 srcs 폴더를 만들어 거기에 둔다.
- Docker 컨테이너 OS는 debian buster를 사용하며 웹서버는 Nginx를 사용한다.
- SSL/TLS 적용
- 리다이렉트 (http->https 이거인듯)
- autoindex on

## 과제 수행

### 환경 구성

- Docker 설치 (생략)
- 폴더 및 dockerfile 생성

```shell
$> mkdir ft_server
$> cd ft_server
$> touch Dockerfile && mkdir src
```

- 요구사항에서 요구하는 debian buster OS 컨테이너 검색
  - go to [도커 허브](https://hub.docker.com)
  - debian 검색 -> buster 태그 검색 : 존재함
    - 태그란? 이미지에 대해 버전 등을 명기하는 것. 일종의 버전이라 할 수 있음 (다른 문서 참조)

### 과제 진행

Dockerfile의 목적은 본인이 원하는 도커 컨테이너를 만드는 것을 자동화 하는 것이다. (리눅스/유닉스의 sh와 비슷하게 특정 작업을 자동화 하는 것이라 할 수 있다.)

따라서 Dockerfile 내부에는 쉘에서 실행할 명령, 파일 복사 명령, 환경변수 지정 명령 등이 들어 있으며 Dockerfile을 작성하기 전에 베이스로 할 이미지를 동작시켜 본인이 원하는 도커 컨테이너를 만들기 위해 명령을 하나씩 실행해보며 Dockerfile을 하나씩 작성해 본다.

### 1. Docker 컨테이너를 쉘에서 실행

docker 컨테이너를 실행하려면 위 OS 이미지를 로컬에 내려받아야 한다.

```shell
$> docker pull debian:buster
```

정상적으로 pull 되었는지 확인한다.

```shell
$> docker images
REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
debian                     buster              1510e8501783        4 weeks ago         114MB
```

pull 되었으면 해당 이미지를 실행해 본다.

```bash
$> docker run -it debian:buster bash
root@db5118498dae:/#
```

해당 명령어의 의미는 이미지를 실행해서 컨테이너로 띄우라는 의미이며 옵션 -it는 쉘을 사용하기 위한 옵션이며 bash는 해당 컨테이너 내의 bash  쉘을 실행하겠다는 의미이다.

Dockerfile에 해당 이미지를 pull 해오겠다는 명령을 작성한다. Dockerfile에 이 내용을 바탕으로 계속 이어서 작성한다.

```dockerfile
FROM debian:buster
```

### 2. Docker 컨테이너 내에서 Nginx 설치 및 실행

#### 간략한 설명

Nginx는 웹 서버 프로그램의 한 종류이다. 웹 서버 프로그램은 Apache, Nginx 등이 있으며 웹 서버는 보통 서버의 파일들을 웹 브라우져가 요구할 때 제공해주는 역할을 한다.

예를 들어 웹 사이트에 접근할 때 페이지 내부 글을 제외하고 사진, 그림, 동영상, 음악 등은 실제로 서버 내부에 존재하는 파일인데 이런 파일들을 클라이언트에 제공해줄 때 웹 서버가 필요하다.

이 외에 다양한 용도가 존재한다. (자세한 내용은 다른 내용 참조)

#### 설치

리눅스/유닉스에서 프로그램을 설치할 때에는 보통 두 가지 방법이 있다.

- 해당 OS에서 주로 사용하는 패키지 매니저를 사용
- 직접 프로그램을 내려받아 수동으로 설치

패키지 매니저는 스마트폰으로 비유하면 앱스토어/플레이스토어와 동일하다. 프로그램의 설치, 제거, 업드레이드를 관리해 주며 비교적 간단하게 원하는 프로그램을 설치할 수 있다.

패키지 매니저를 사용하지 않으면 직접 프로그램을 내려받아 설치해야 하는데 해당 방법은 프로그램 개발사가 만든 방법대로 설치해야 하기 때문에 방법도 다양하며 설치방법이 복잡하다.

ft_server 과제에서 패키지 매니저를 사용하는 데 제한을 두지 않았으므로 데비안에서 사용하는 패키지 매니저 (apt)를 사용해서 설치할 것이다.

```bash
$> apt update -y # 패키지 매니저가 설치 가능한 리스트를 새로 업데이트 함. (-y 옵션은 동의 구하지 않고 실행)
$> apt install -y nginx # nginx 설치
```

-y 옵션을 사용하지 않으면 사용자에게 동의를 구하고 설치한다.

설치가 되었는지 확인해본다.

```shell
$> nginx -v
nginx version: nginx/1.14.2
```

nginx가 정상적으로 실행되는지 확인해야 한다. 하지만 nginx는 컨테이너 내부에서 동작되는데 외부(로컬)와 내부(컨테이너) 사이 간 네트워크 통신을 할 수 있도록 해당 컨테이너를 commit 한 후 nginx가 사용할 포트를 열고 다시 실행해야 한다.

```shell
(docker)$> exit # 컨테이너 쉘 빠져나감
(local)$> docker ps -a # 실행했던 컨테이너의 ID를 알아냄
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
db5118498dae        debian:buster       "bash"              About an hour ago   Exited (0) 3 seconds ago                       boring_mestorf
(local)$> docker commit db5118498dae ft_server:0.1 # 컨테이너를 commit하여 이미지화 (ft_server:0.1)
sha256:ce8204751b0f1c51b880584c6ad93e8aba677ffd83c743b41b082d71dc0f1b20
(local)$> docker run -it -p 80:80 ft_server:0.1 bash # -p [host:컨테이너]
(docker)$> 
```

80번 포트를 개방했으면 nginx를 실행해 본다.

```shell
$> service --status-all
 [ ? ]  hwclock.sh
 [ - ]  nginx
$> service nginx status
[FAIL] nginx is not running ... failed!
$>  service nginx start
[ ok ] Starting nginx: nginx.
```

service 명령어는 설치된 서비스 프로그램을 관리하는 명령어이다. service는 /etc/init.d 내부에 있는 데몬 스크립트를 관리한다. (자세한 설명은 다른 내용 참조)

service 명령어를 이용해 nginx 서비스가 정상적으로 등록이 되었는지, 현재 nginx 서비스가 실행중인지 확인하며 실행중이 아니라면 실행을 시킨다.

정상적으로 nginx가 실행되면 [http://127.0.0.1](http://127.0.0.1) 으로 접근하여 Welcome to nginx! 페이지에 접속되는지 확인한다.

Dockerfile에 작성할 명령은 다음과 같다.

```dockerfile
RUN apt update -y \
&& apt install -y nginx
# nginx 실행은 Dockerfile 마지막에 삽입해야 한다.
CMD service nginx start
```

Dockerfile의 RUN 명령은 해당 컨테이너에서 실행할 명령을 실행하는 것이다. 궂이 &&로 apt 명령을 묶는 이유는 RUN 명령을 실행할 때마다 image layer가 하나씩 생기기 때문에 파편화가 생기기 때문에 패키지 설치같은 명령은 RUN 명령을 이용해 실행한다.

CMD 명령어는 뒤에 오는 명령어가 컨테이너가 생성될 때 실행된다.

### 3. PHP + Nginx 연동

#### 간략한 설명

PHP는 동적인 웹 페이지를 만들기 위해 사용되는 서버사이드 스크립트 언어이다. C언어 기반으로 개발되었다.

문법이 비교적 단순하다는 특징이 있다. (자바기반인 JSP처럼 프로젝트 구조나 기능 구현이 복잡하지 않다)

과거에는 한국 내에서 PHP가 많이 쓰였지만 최근에는 JSP 기반으로 많이 사용한다.

PHP를 설치하는 이유는 Wordpress, phpmyadmin 두 프로그램이 PHP 기반으로 동작되기 때문이다.

PHP-FPM은 Nginx 등에 PHP를 연동할 때 사용한다.

만약에 PHP를 설치하지 않고 Nginx 서버에 php 파일에 접근하면 php 소스 코드가 그대로 나온다.

PHP-FPM은 Nginx에서 php 파일에 요청이 있을 시 PHP 스크립트를 컴파일 한 결과를 응답해주는 프로그램이라고 생각하면 이해가 쉽다.

기존 흐름이 다음과 같다면

(브라우저에서 파일요청) - (nginx에서 요청 수신) - (nginx에서 응답) - (브라우저에서 파일 출력)

php 파일에 대해선 다음과 같다.

(브라우저에서 파일요청) - (nginx에서 요청 수신) - (PHP-FPM) - (nginx에서 응답) - (브라우저에서 파일 출력)

#### PHP-FPM 설치

```bash
# 2020-11 기준 php 7.3버전이 설치된다.
$> apt install php-fpm
# 또는
$> apt install php7.3-fpm
```

현재는 php 최신버전이 7.3이기 때문에 위와 아래와 동일한 효과가 있다.

특정 버전을 설치해야 하거나 명확한 버전 관리를 위한다면 아래 방법으로 설치해야 한다.

설치가 되었는지 확인해본다.

```shell
$> php -v
PHP 7.3.19-1~deb10u1 (cli) (built: Jul  5 2020 06:46:45) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.19, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.19-1~deb10u1, Copyright (c) 1999-2018, by Zend Technologies
```

php-fpm은 서비스 프로그램으로 실행되어야 하기 때문에 정상적으로 서비스에 등록되었는지 확인해보는것도 좋다.

```shell
$>  service --status-all
 [ ? ]  hwclock.sh
 [ + ]  nginx
 [ - ]  php7.3-fpm
 [ - ]  procps
```

#### PHP-FPM과 Nginx 연동

Nginx의 설정은 nginx.conf 파일을 이용해 설정하며 해당 파일은 find 명령을 이용해 찾는다. (보통 /etc/nginx/nginx.conf 에 있다.)

nginx.conf 의 자세한 설정 방법은 생략한다.

```shell
$> find / -name "nginx.conf"
/etc/nginx/nginx.conf
```

해당 파일을 열람하기 위해 vim을 설치해야 한다. (Dockerfile에는 필요 없다.)

```shell
$> apt install vim
$> cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak # 설정파일을 수정할 때에는 왠만하면 백업파일을 만든다.
$> vi /etc/nginx/nginx.conf
```

nginx.conf 파일을 열람하면 http 부분 내에 주석 처리 된 Virtual Host Setting가 있다. 해당 설정은 가상 호스트를 설정하는 것이며 include 되는 구문이 두 줄이 있을것이다. (해당 설정이 뭘 하는 것인지에 대해선 생략한다.)

include /etc/nginx/conf.d/*.conf 구문은 /etc/nginx/conf.d/ 경로에 아무것도 없기 때문에 무효하고 include /etc/nginx/sites-enabled 구문은 /etc/nginx/sites-enabled 폴더 내에 default 파일이 있기 때문에 해당 파일을 include 한다.

Nginx 연동 및 뒤에서 진행할 autoindex 설정, SSL 설정은 해당 파일에서 진행하므로 include 할 파일을 별도로 만들어서 nginx.conf 파일을 수정하거나 default 파일을 직접 수정한다.

default 파일을 직접 수정하면  default 파일의 백업본을 만드는 것이 좋다.

default 파일 내부를 보면 PHP 설정 구간이 있는데 기본값으로 해당 구문은 주석처리되어 있다.

```
	# pass PHP scripts to FastCGI server
	#
	#location ~ \.php$ {
	#	include snippets/fastcgi-php.conf;
	#
	#	# With php-fpm (or other unix sockets):
	#	fastcgi_pass unix:/run/php/php7.3-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	#}
```

PHP-FPM은 유닉스 소켓 (유닉스 계열에서 사용하는 ipc의 일종, ipc - 내부 프로세스 간 통신)을 사용하므로 해당 구문과 중괄호, include의 주석을 제거한다.

변경했으면 저장 후 Nginx를 재시작 하며 php-fpm 서비스도 새로 시작한다.

```shell
$> service nginx restart
[ ok ] Restarting nginx: nginx.
$> service php7.3-fpm start
$> service php7.3-fpm status
[ ok ] php-fpm7.3 is running.
```

정상적으로 php와 연동되서 구동되는지 확인한다.

```php
<?php
phpinfo();
?>
```

 편집기로 해당 코드를 삽입한후 phpinfo.php 파일명으로 /var/www/html 경로에 저장한다.

저장 후 [http://127.0.0.1/phpinfo.php](http://127.0.0.1/phpinfo.php) 경로에 웹브라우저로 접근해서 PHP 관련 정보가 잘 출력되면 잘 설치된 것이다.

Dockerfile에 추가할 명령은 다음과 같다.

```dockerfile
RUN apt update -y \
&& apt install -y nginx php7.3-fpm
CMD service nginx start && service php-fpm7.3 start
```

apt를 이용한 설치 및 실행할 코드는 nginx와 중복이므로 php 관련 명령만 추가한다.

### 4. SQL DBMS 설치와 유저 생성

Database를 설치해야 하는데 Wordpress, phpmyadmin은 MySQL (MariaDB) 기반으로 동작되기 때문에 MySQL (MariaDB) 를 사용해야 한다.

MySQL ,MariaDB 중 무엇을 설치해도 명령어 등을 거의 동일하므로 무엇을 설치해도 큰 상관은 없다.

```bash
$> apt install mariadb-server
```

MySQL (MariaDB)은 서비스 프로그램으로 실행되어야 하기 때문에 정상적으로 서비스에 등록되었는지 확인해보는것도 좋다.

```shell
$>  service --status-all
 [ ? ]  hwclock.sh
 [ - ]  mysql
 [ + ]  nginx
 [ - ]  php7.3-fpm
 [ - ]  procps
```

설치가 완료되면 정상적으로 DB에 접근가능한지 확인해본다.

```bash
$> mysql
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
```

mysql 명령어는 MySQL (MariaDB) 서버에 접근할 수 있도록 해주는 프로그램인데 MySQL (MariaDB) 서버가 구동중이 아니라 접근이 안되는 것이다. MySQL (MariaDB) 서버를 구동한다.

서버 구동 후 DB 서버에 등록된 사용자를 확인한다.

```bash
$> service mysql start
[ ok ] Starting MariaDB database server: mysqld.
$> mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 51
Server version: 10.3.25-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> select user, host from user;
+------+-----------+
| user | host      |
+------+-----------+
| root | localhost |
+------+-----------+
1 row in set (0.001 sec)

```

처음 설치하면 기본 유저는 보통 root밖에 없다. 본인이 사용할 사용자를 만들어주어야 한다.

예를 들어 wordpress용으로만 사용한다면 그에 대한 사용자를 만든다거나 실제 해당 DB에 접근하는 사용자를 구분해서 만들거나 하면 된다.

```sql
# 사용자 명과 비밀번호가 joopark인 계정을 만든다. localhost는 해당 계정이 접근할수 있는 네트워크를 의미한다.
# 만약 특정 네트워트에서 접근한다면 해당 아이피를 적으면 되고 모든곳에서 접근을 허용한다면 %을 기입한다.
MariaDB [mysql]> create user 'joopark'@'localhost' identified by 'joopark';
# joopark 사용자에 대한 모든 접근 권한을 부여한다.
MariaDB [mysql]> grant all privileges on *.* to joopark@'localhost';
# 변경 사항을 적용한다.
MariaDB [mysql]> flush privileges;
```

### 5. SQL-PHP 연동 및 phpMyAdmin 테스트

MySQL (MariaDB)과 PHP를 연동하려면 해당 패키지를 설치해야 한다.

```bash
# 2020-11 기준 7.3버전이 설치된다.
$> apt install php-mysql
# 또는
$> apt install php7.3-mysql
```

해당 패키지는 PHP 단에서 동작하는 PHP-MySQL 연동 모듈이다.

phpMyAdmin은 패키지로 내려받지 않고 직접 파일을 내려받아서 사용한다.

외부 서버의 파일을 내려받을 때 wget 혹은 curl 명령어를 사용한다. 어느 것을 사용하던 debian:buster 이미지에 포함되어 있지 않기 때문에 설치해서 사용해야 한다.

```bash
$> apt install wget
$> wget -P /var/www/html/ https://files.phpmyadmin.net/phpMyAdmin/4.9.7/phpMyAdmin-4.9.7-all-languages.tar.gz
$> tar -zxvf /var/www/html/phpMyAdmin-4.9.7-all-languages.tar.gz -C /var/www/html/phpmy
```

작업 후 로컬 브라우저에서 [http://127.0.0.1/phpMyAdmin-4.9.7-all-languages/index.php](http://127.0.0.1/phpMyAdmin-4.9.7-all-languages/index.php) 에 접근해서 위에서 만든 계정과 비밀번호로 로그인이 잘 되면



### 100000. Autoindex on

autoindex는 웹 서버의 디렉토리에 접근할 때 디렉토리 내부 파일들을 목록으로 출력해주는 웹 서버의 기능을 말한다.

