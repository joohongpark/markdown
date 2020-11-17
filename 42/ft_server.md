# ft_server 요구사항

## 목적

- Docker 컨테이너를 이용해 Wordpress, phpmyadmin, SQL Database 환경을 구성한다.
- Docker 컨테이너 사용 및 자동화를 익힌다. 또 간단한 웹 서비스를 만들어 본다.

## 요구사항

- 하나의 Docker 컨테이너에서만 구동되어야 한다.
- Wordpress, phpmyadmin, SQL Database 환경을 docker 컨테이너 내부에 셋팅한다.
- 위 서비스를 쉘 스크립트 및 dockerfile로 자동 설치/실행시킨다.
- 리포 루트 디렉토리에 Dockerfile을 두고, 필요한 파일(WordPress도?)들은 srcs 폴더를 만들어 거기에 둔다.
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

autoindex는 웹 서버의 디렉토리에 접근할 때 디렉토리 내부 파일들을 목록으로 출력해주는 웹 서버의 기능을 말한다. 하지만 해당 기능을 남용하면 웹 서버 내부 파일 리스트가 그대로 보이므로 보안이 허술해질 수 있다.

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

[http://127.0.0.1](http://127.0.0.1) 에 접속하면 출력되는 html은 웹 서버 루트 디렉토리의 Index 파일이다. 루트 디렉토리가 어디를 가리키는지는 후술한다.

Dockerfile에 작성할 명령은 다음과 같다.

```dockerfile
RUN apt update -y \
&& apt install -y nginx
# nginx 실행은 Dockerfile 마지막에 삽입해야 한다.
CMD service nginx start
```

Dockerfile의 RUN 명령은 해당 컨테이너에서 실행할 명령을 실행하는 것이다. 궂이 &&로 apt 명령을 묶는 이유는 RUN 명령을 실행할 때마다 image layer가 하나씩 생기기 때문에 파편화가 생기기 때문에 패키지 설치같은 명령은 RUN 명령을 이용해 실행한다.

CMD 명령어는 뒤에 오는 명령어가 컨테이너가 생성될 때 실행된다.

### 3. PHP + Nginx 연동, Autoindex on

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

default 파일을 직접 수정하면 default 파일의 백업본을 만드는 것이 좋다.

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

#### index.php 추가 및 Autoindex on

php 기반으로 만들어진 웹 프로그램을 실행할 때 보통 index.php에 접근하는 작업이 많다. 따라서 url로 특정 디렉토리에 접근할 때 해당 폴더가 php 기반 프로젝트일 때 자동으로 index.php가 반환되게 만든다. (해당 작업을 서버단에서 수행하지 않으면 php 기반으로 만들어진 웹 사이트에 접근할때 www.사이트.com/index.php 와 같이 타이핑해야 한다.)

위와 동일하게 default 파일을 수정한다. 아마 중간에 add index.php ~~~~라고 써있는 주석이 있을것이다.

autoindex 기능은 index 파일이 없을 때에 대응해 해당 디렉토리를 목록으로 출력하는 기능이다. 해당 기능이 켜져있지 않다면 폴더에 접근할 때 404-page not found 오류가 뜰 것이다.

해당 기능은 default 파일에 autoindex on; 구문을 삽입하면 작동된다.

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

### 5. SQL-PHP 연동 및 phpMyAdmin 적용

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
$> wget -O /var/www/html/phpmy.tar.gz https://files.phpmyadmin.net/phpMyAdmin/4.9.7/phpMyAdmin-4.9.7-all-languages.tar.gz
$> tar -zxvf /var/www/html/phpmy.tar.gz -C /var/www/html
```

작업 후 로컬 브라우저에서 [http://127.0.0.1/phpMyAdmin-4.9.7-all-languages/index.php](http://127.0.0.1/phpMyAdmin-4.9.7-all-languages/index.php) 에 접근해서 위에서 만든 계정과 비밀번호로 로그인이 잘 되면 연동은 성공한 것이다.

접속하면 페이지 마지막에 여러 오류가 뜰 것인데 하나씩 해결해 보자.

먼저 mbstring PHP extension 오류가 뜬다. 해당 오류는 PHP에서 non-ASCII 문자열을 다루지 못해 별도 PHP 확장 모듈을 설치해야 원활하게 유니코드 등을 다룰 수 있는데 해당 모듈이 설치되지 않아 오류가 나는 것이다.

해당 확장모듈을 설치한다. php-mysql처럼 apt로 설치할 수 있다.

```bash
# 2020-11 기준 7.3버전이 설치된다.
$> apt install php-mbstring
# 또는
$> apt install php7.3-mbstring
```

설치 후 php 데몬을 재시작하면 해당 오류는 사라진다.

다음 오류를 해결해보자. blowfish_secret이 필요하다는 오류가 뜬다.

blowfish_secret은 phpmyadmin이 사용하는 고유의 랜덤 키 값이다. 중요한 것은 아니지만 blowfish라는 알고리즘 기반이며 최대 46바이트인 임의의 아스키코드 값을 지정한다.

config.inc.php 파일 내에 $cfg['blowfish_secret'] 값에 키 값을 집어 넣으면 되며 아무 랜덤 값이나 집어 넣으면 된다. 나는 랜덤 키 생성 사이트를 이용했다. [링크](http://www.question-defense.com/tools/phpmyadmin-blowfish-secret-generator)

config.inc.php 파일은 처음 내려받을 때 존재하지 않으며 config.sample.inc.php 파일이 존재한다. 해당 파일을 카피해서 설정에 맞게 바꾼 후 config.inc.php 파일로 이름을 변경해 사용한다.

```bash
# config.inc.php 생성
$> cp /var/www/html/phpMyAdmin-4.9.7-all-languages/config.sample.inc.php /var/www/html/phpMyAdmin-4.9.7-all-languages/config.inc.php
# $cfg['blowfish_secret'] 항목에 {^QP+-(3mlHy+Gd~FE3mN{gIATs^1lX+T=KVYv{ubK*U0V 삽입
$> vi /var/www/html/phpMyAdmin-4.9.7-all-languages/config.inc.php
```

추후에 dockerfile을 이용해 자동화 할 때에는 config.inc.php 파일을 미리 만들어 놓거나 patch를 이용하거나 한다. (추후에 기술)

해당 작업을 하면 blowfish_secret 에러 메시지는 사라진다. (php 데몬을 재 시작할 필요는 없다.)

마지막으로 $cfg[]TempDir''(./tmp/)에 액세스할 수 없다는 에러가 있다. 해당 에러는 웹 서버를 구동하는 계정으로 phpmyadmin 파일에 접근할 수 없어 오류가 나는 것이다. 해당 오류를 해결하기 위해서는 phpmyadmin 파일/폴더의 소유자와 웹 서버 UID가 매칭이 되는지, 매칭이 된다면 해당 파일/폴더의 권한이 허용 되어있는지 확인해야 한다.

먼저 ps 명령어로 웹 서버 데몬의 UID를 확인한다.

```bash
$> ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 Nov15 pts/0    00:00:00 bash
root       388     0  0 01:48 pts/1    00:00:00 bash
root      6630     1  0 03:59 ?        00:00:00 nginx: master process /usr/sbin/nginx
www-data  6631  6630  0 03:59 ?        00:00:00 nginx: worker process
www-data  6632  6630  0 03:59 ?        00:00:00 nginx: worker process
www-data  6633  6630  0 03:59 ?        00:00:00 nginx: worker process
www-data  6634  6630  0 03:59 ?        00:00:00 nginx: worker process
www-data  6636  6630  0 03:59 ?        00:00:00 nginx: worker process
www-data  6637  6630  0 03:59 ?        00:00:00 nginx: worker process
root      7522     1  0 07:39 pts/1    00:00:00 /bin/sh /usr/bin/mysqld_safe
mysql     7639  7522  0 07:39 pts/1    00:00:48 /usr/sbin/mysqld --basedir=/usr --datadir=/var/lib/mysql --plugin-dir=/usr/lib/x86_64-linux-gnu/mariadb1
root      7640  7522  0 07:39 pts/1    00:00:00 logger -t mysqld -p daemon error
root     11403     1  0 11:20 ?        00:00:00 php-fpm: master process (/etc/php/7.3/fpm/php-fpm.conf)
www-data 11404 11403  0 11:20 ?        00:00:00 php-fpm: pool www
www-data 11405 11403  0 11:20 ?        00:00:00 php-fpm: pool www
root     11464   388  0 12:21 pts/1    00:00:00 ps -ef
```

다음 엑세스하고자 하는 폴더의 퍼미션, 소유자, 그룹를 알아보자.

```bash
$> ls -l /var/www/html/
total 9968
-rw-r--r--  1 root root      612 Nov 15 07:54 index.nginx-debian.html
drwxr-xr-x 12 root root     4096 Nov 16 11:36 phpMyAdmin-4.9.7-all-languages
-rw-r--r--  1 root root 10190261 Oct 15 18:50 phpMyAdmin-4.9.7-all-languages.tar.gz
-rw-r--r--  1 root root       20 Nov 15 14:04 phpinfo.php
drwxr-xr-x  2 root root     4096 Nov 15 11:59 test
-rw-r--r--  1 root root        0 Nov 15 11:59 test.txt
```

웹 서버 nginx의 데몬의 UID는 www-data임을 알 수 있다.

퍼미션은 유저에게 읽기, 쓰기 허가. 그외에는 읽기만 허가되어 있으며 소유자와 그룹은 모두 root로 되어 있다. 접근하는 데몬의 주체는 www-data인데 phpmyadmin 파일들의 소유자는 root이기 때문에 소유자와 그룹 모두 불일치하여 쓰기 작업을 할 수 없어 오류가 나는 것이다.

/var/www/html/ 경로의 소유자를 www-data로 변경한다. (www-data는 데비안 OS에 기본적으로 존재하는 사용자로 웹 서버를 위한 사용자이다.) 유저에게는 읽기/쓰기가 허가되어 있으므로 퍼미션을 별도로 변경할 필요는 없다.

```shell
$> chown -R www-data /var/www/html/
```

해당 명령어를 수행하고 ls -l 명령어로 확인해 보면 소유자가 www-data로 변경되어 있다. 해당 작업을 하면 액세스 에러 메시지는 사라진다. (php 데몬을 재 시작할 필요는 없다.)

### 6. wordpress 설치

wordpress는 php 기반의 웹사이트/블로그 프로그램이다. wordpress를 웹 서버에 설치하기만 하면 나만의 웹 사이트를 만들 수 있다.

설치 절차는 다음 [링크](https://ko.wordpress.org/txt-install/) 에 명시되어 있다.

링크에 따르면 DB명을 알아야 한다고 하니 wordpress 용의 DB를 새로 만드는게 좋을 것이다.

phpmyadmin으로 신규 db를 만들고 계정을 추가하고 권한을 해당 데이터베이스에 할당해 보자. (Dockerfile에서는 phpmyadmin을 사용하지 못한다. 따라서 본인이 무슨 작업을 했는지 기록해서 쿼리문으로 적을 수 있어야 한다.)

```bash
# wordpress를 위한 계정, db 만드는 쿼리문
MariaDB [(none)]> create user wp_root@'localhost' identified by 'joopark';
MariaDB [(none)]> create database wp_db default CHARACTER SET UTF8;
MariaDB [(none)]> grant all privileges on wp_db.* to wp_root@'localhost';
MariaDB [(none)]> flush privileges;
$> wget -O /var/www/html/wordpress.tar.gz https://wordpress.org/latest.tar.gz
$> tar -zxvf /var/www/html/wordpress.tar.gz -C /var/www/html
```

나머지 설치 절차는 생략한다.

설치 절차를 정상적으로 진행했으면 wordpress가 정상적으로 실행된다.

### 7. SSL/TLS 적용 및 리다이렉트

SSL/TLS는 인터넷 트래픽을 암호화하기 위한 기술이다. (보통 HTTP 프로토콜을 암호화한다. 하지만 TCP 프로토콜을 사용하면 별도 제한은 없다.) SSL 후속으로 나온 것이 TLS이지만 보통 SSL/TLS로 묶어서 분류한다.

TLS 기술은 최근의 웹사이트에서는 대부분 사용되는 기술이며 웹 브라우저의 주소가 https로 시작한다면 해당 웹 서버에서는 TLS를 이용해 페이지를 암호화한 것이다.

TLS에 대한 자세한 설명을 하기에 제한되지만 간략하게 설명해보려 한다.

#### SSL/TLS란?

TLS의 핵심은 비대칭키 암호/ 대칭키 암호 둘 다 사용하는 것과 인증서 발급 기관이 있는 것이다.

대칭키 암호는 암호화/복호화 키가 같은 것이고 비대칭키 암호는 암호화/복호화 키가 서로 다른 것이다. 따라서 대칭키 암호로 암호화 한 내용은 키만 알고 있다면 언제든지 복호화가 가능하다. 하지만 비대칭키 암호는 복호화 키를 모른다면 암호화 키를 알아도 절대로 복호화 할 수 없다.

하지만 대칭키 암호는 암호화/복호화 시 속도가 빠르고 비대칭키 암호는 암호화/복호화 속도가 느리다.

만약에 A와 B가 메세지를 암호화해서 송/수신할 때 비대칭키 암호, 대칭키 암호 중 무엇을 사용할 지 고민한다면 속도가 느리지만 보안이 매우 높은 비대칭키 암호와 속도가 빠르지만 보안이 상대적으로 낮은 대칭키 암호를 사용할 지 고민할 것이다. 이상적인 상황은 메시지 모두 비대칭키 암호를 사용하는 것이다. 하지만 속도가 느린 단점이 있어 메시지 암호화는 대칭키 암호를 사용하되 대칭키 암호의 "키"에 대해서만 비대칭키 암호를 사용한다.

어차피 대칭키 암호로 암호화된 메세지도 키 자체를 모르면 복호화 할 수 없기 때문이다. 따라서 키만 비대칭키 암호를 사용해 메시지 전체에 대해 비대칭키 암호화와 같은 효과를 내는 것이다.

인증서 발급 기관이 있는 이유는 A와 B가 통신할 때 A, B를 다른 사람이 도용하지 못하도록 하는 장치이다. 예를 들어 기관 C에서 A와 B가 본인이라는 고유의 인증서를 발급해주면 다른 사람이 C에게 A와 B가 진짜 A, B인지 물어볼 수 있다.

따라서 네이버 등에 대한 피싱 사이트를 유심히 보면 HTTPS가 아니라 HTTP를 사용하며 피싱 사이트 구별법 중 HTTP인지 HTTPS인지 구별하는 방법이 있다. 피싱 사이트를 인증해주는 기관은 없을 것이기 때문이다.

TLS에 대해 더 알고 싶다면 다른 글들을 참조하라.

#### 대략적인 순서

1. 개인키 생성

   본인임을 증명할 개인키를 하나 만든다. 이 키를 이용해서 인증서 발급 등을 할 것이기 때문에 분실하면 안된다.

   개인키는 비대칭 암호를 이용해 만들며 openssl을 이용해서 만들 수 있다.

   ```shell
   $> openssl genrsa -out priv.key 2048 # RSA 개인키를 2048 bit 길이로 만듬
   $> openssl rsa -in priv.key -pubout -out pub.key # 개인키로부터 공용 키를 만듬 (반드시 만들 필요는 없음)
   ```

2. CSR (Certificate Signing Request) 생성

   CSR은 SSL 인증서를 발급받고자 할 때 사용하는 일종의 신청서이다. 인증서 발급 기관(CA라고 한다.)은 CSR 파일을 받아 서명을 해 인증서를 발급해 준다.

   인증서 발급 기관은 신청자와 CSR 파일 내용 (신청자의 국가, 시, 구, 기관명, 조직명, 도메인명)을 읽어들여 해당 내용에 대해 검토하고 발급해 준다.

   ```shell
   # 다음과 같이 쓰면 입력이 필요한 정보를 물어본다.
   $> openssl req -new -key priv.key -out req.csr # 개인키를 가지고 CSR을 생성한다.
   # -subj 옵션으로 입력이 필요한 정보를 미리 입력할 수도 있다.
   $> openssl req -new -subj "/C=KR/ST=Gyeonggi-do/L=Suwon-si/O=42Seoul/OU=42Seoul/CN=joopark" -key priv.key -out req.csr
   ```


3. CSR 파일을 인증 기관에 전송

   생성된 csr 파일을 인증서 발급 기관에 보내서 인증서를 발급 받는다.

   인증서 발급 시 실제 인증서 발급 기관에 신청하여 발급받는 것이 원칙이지만 내 자신이 CA라 생각하고 만들어서 발급해 본다.

   1. Root CA 전용 개인키 생성

      ```shell
      $> openssl genrsa -out MyrootCA.key 2048 # RSA 개인키를 2048 bit 길이로 만듬
      ```

   2. Root CA 인증서 생성

      ```shell
      $> openssl req -x509 -new -subj "/C=KR/ST=Gyeonggi-do/L=Suwon-si/O=42Seoul/OU=root/CN=root" -nodes -key MyrootCA.key -days 3650 -out MyrootCA.pem # 유효기간 10년인 Root CA 인증서를 만듬
      ```

   3. CSR을 통한 Root CA로 서명된 인증서 발급

      ```shell
      $> openssl x509 -req -in req.csr -CA /MyrootCA.pem -CAkey MyrootCA.key -CAcreateserial -out gen.crt -days 3650
      ```


4. 생성된 키를 서버에 적용

#### 발급받은 키를 Nginx 서버에 적용

nginx.conf 내부에 Virtual Host Setting은 /etc/nginx/sites-enabled/default 를 import해서 설정하기 때문에 해당 파일을 변경한다.

발급받은 키 (위 gen.crt)와 본인의 개인 키 (위 priv.key)를 해당 파일에 등록한다.

#### 리다이렉트

리다이렉트는 특정 url로 접근할 때 다른 url로 연결시키는 것을 의미한다.

리다이렉트는 HTTP 표준으로 정의되어 있다.

보통 SSL/TLS가 적용된 웹 사이트는 http로 들어오는 요청을 https로 리다이렉트 시킨다. 따라서 http로 들어오는 요청을 https로 리다이렉트 시킨다.

http는 포트 80번을 이용하고 https는 포트 443번을 이용한다. 이도 위 파일을 적당히 수정해 적용한다.

### 7. Dockerfile 작성

1번부터 6번까지의 작업을 스크립트로 자동화 시켜본다. 이후 스크립트로 자동화 된 동작을 dockerfile에 기록하면 된다.

위 작업들을 간소화시켜 쉘 명령어들을 나열한 내용은 다음과 같다.

```shell
# apt 패키지 매니저 업데이트
apt update -y

# 필요로 하는 프로그램 패키지 설치 (nginx, php, php 모듈, mariadb)
apt install -y nginx php7.3-fpm php7.3-mysql php7.3-mbstring mariadb-server patch --no-install-recommends

# phpmyadmin, wordpress 복사해온 후 압축 해제
cp [원본 경로]/phpmy.tar.gz /var/www/html/
cp [원본 경로]/wordpress.tar.gz /var/www/html/
mkdir /var/www/html/phpmy
mkdir /var/www/html/wordpress
tar -zxvf /var/www/html/phpmy.tar.gz --strip-components=1 -C /var/www/html/phpmy
tar -zxvf /var/www/html/wordpress.tar.gz --strip-components=1 -C /var/www/html/wordpress

# nginx 설정 patch 혹은 copy
cp /etc/nginx/sites-enabled/default

# phpmyadmin 설정 patch 혹은 copy
cp /var/www/html/phpmy

# wordpress 설정 patch 혹은 copy
cp /var/www/html/wordpress

# 미리 만들어 놓은 인증서 복사
cp /etc/certs

# 서버 파일 소유자 변경 (root -> www-data)
chown -R www-data /var/www/html/

# sql 쿼리문 실행
service mysql start
cat test.sql | mysql -u root

## php, nginx 데몬 실행
service php7.3-fpm start
service nginx start
```

이 명령들을 내에 COPY, RUN, CMD(혹은 ENTRYPOINT) 등을 사용하여 dockerfile을 작성한다.