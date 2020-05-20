# Maven 기초

## 1. Maven이란?

Maven은 소프트웨어 프로젝트 관리도구 (특히 자바)이다. C에 Make를 비롯한 프로젝트 빌드 관리도구가 있다면 자바에서는 Maven (혹은 Gradle)이 사용된다.

자바 기반 프로젝트의 내/외부 의존 라이브러리 관리, 코드 테스트 및 빌드를 용이하게 할 수 있다.

부가적인 요소 및 다른 중요한 요소도 많지만, 필자가 생각하는 가장 큰 구성 요소는 "의존 라이브러리", "플러그인"이라고 생각한다.

Maven 프로젝트는 "프로젝트 객체 모델 - POM" 구조로 관리되며 해당 모델 구조는 XML 기반으로 작성한다.

## 2. Maven 버전 및 Maven에서 사용하는 자바 Path 및 버전 확인

```shell
$mvn --version
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /usr/local/Cellar/maven/3.6.3_1/libexec
Java version: 13.0.2, vendor: N/A, runtime: /usr/local/Cellar/openjdk/13.0.2+8_2/libexec/openjdk.jdk/Contents/Home
Default locale: ko_KR, platform encoding: UTF-8
OS name: "mac os x", version: "10.15", arch: "x86_64", family: "mac"
```

Maven이 사용하는 자바 버전 등을 확인할 수 있다.

## 3. Maven 프로젝트 생성 및 pom.xml에 대한 간략한 설명

cli 상에서 Maven 프로젝트를 생성하는 예제 명령어이다.

```shell
$ mvn archetype:generate \
      -DgroupId=com.profq.app \
      -DartifactId=profq_app \
      -DarchetypeArtifactId=maven-archetype-quickstart \
      -DarchetypeVersion=1.4
      -DinteractiveMode=false
```

상기 명령어를 실행하면 profq_app 폴더 내에 pom.xml 및 src 폴더를 생성한다.

조금 더 자세히 설명하면 프로젝트를 생성하기 위한 명령어 mvn archetype:generate을 실행한 것이고

해당 명령어에 대한 매개 변수를 -D 형태로 입력한 것이다.

해당 명령어 및 매개변수를 통해 특정 탬플릿의 프로젝트가 생성이 되며 파일 밋 디렉토리를 생성한다.

생성된 src 폴더 내에는 표준 디렉토리 구조에 따라 기본 실행 소스(src/main/java) 및 테스트 소스(src/test/java)가 생성된다.

(Maven 표준 디렉토리 구조는 해당 url에 명기되어 있다. https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

pom.xml 파일은 Maven 프로젝트의 중심이 되는 환경설정 파일이다.

명령어를 통해 생성된 해당 pom.xml 파일의 핵심 부분을 간추리면 다음과 같다.

```xml
<project>
	<modelVersion>4.0.0</modelVersion> <!-- pom 스키마 버전 -->
  <groupId>com.profq.app</groupId> <!-- 해당 프로젝트의 그룹id -->
  <artifactId>profq_app</artifactId> <!-- 프로젝트 고유id -->
  <versiom>1.0-SNAPSHOT</version> <!-- 해당 프로젝트 버전 -->
  <name>profq_app</name> <!-- 프로젝트 이름 -->
  
  <properties>
  	<!-- 필요시 인코딩 및 기타 속성값 기재 -->
  </properties>
  
  <dependencies> <!-- 해당 프로젝트에서 사용되는 라이브러리 기입 -->
  	<dependency>
    	<!-- junit이라는 자바 unit testing framework (자바 단위 테스팅 라이브러리) -->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  
  <build>
    <plugins>
    	<plugin>
        <!-- 빌드 시 사용되는 플러그인 기재 -->
        <!-- 이하 생략 -->
      </plugin>
    </plugins>
  </build>
</project>
```

Maven은 프로젝트를 생성할 때, 탬플릿을 기반으로 생성하게 된다. 상기 명령어에서 적용된 탬플릿은 maven-archetype-quickstart 이다. 탬플릿에 따라 src 하위 디렉토리 혹은 라이브러리(디펜던시)가 변동될 수 있다.

## 4. Maven Goal(목표) 및 plugin 태그

해당 프로젝트에서 컴파일 하기 위한 명령은 다음과 같다.

```shell
$ mvn compile
```

해당 명령 실행 시 ./target/classes 디렉토리 하위에 컴파일 된 클래스 파일을 생성한다.

해당 프로젝트에서 패키지 (jar 등)를 생성하기 위한 명령은 다음과 같다.

```shell
$ mvn package
```

해당 명령 실행 시 ./target/[artifactId]-[version].jar 파일을 생성한다.

상기 명령 (컴파일 및 패키지)은 Maven에서 goal-목표 이라고 칭한다.

상기 goal을 이용해서 생성된 결과물은 단순히 실행되지 않는다. class path 혹은 메인 클래스를 지정해야 실행된다.

Maven에서 mvn 명령어 뒤에 붙는 키워드는 goal 이다.

```shell
$ mvn clean
$ mvn compile
$ mvn package
$ mvn archetype:generate
```

상기 명령어의 예는 4가지 각기 다른 goal을 실행한 것이다.

package goal 을 실행하여 패키지를 생성하여도 해당 패키지를 바로 실행할 수 없다. 실행 시 자바에 별도 인수(실행할 클래스 등)를 집어 넣어야 동작하며 별도의 인수 없이 동작하는 패키지를 생성하려면 별도의 플러그인을 추가하여 별도의 goal을 지정하여야 한다.

프로젝트에서 특정 클래스를 실행할 수 있는 goal을 만들어 볼 것이다.

exec-maven-plugin을 사용할 것이며 상기 pom.xml에서 plugin 태그에 추가하는 과정은 다음과 같다.

```XML
<project>
	<modelVersion>4.0.0</modelVersion> <!-- pom 스키마 버전 -->
  <groupId>com.profq.app</groupId> <!-- 해당 프로젝트의 그룹id -->
  <artifactId>profq_app</artifactId> <!-- 프로젝트 고유id -->
  <versiom>1.0-SNAPSHOT</version> <!-- 해당 프로젝트 버전 -->
  <name>profq_app</name> <!-- 프로젝트 이름 -->
  
  <properties>
  	<!-- 생략 -->
  </properties>
  
  <dependencies>
    <!-- 생략 -->
  </dependencies>
  
  <build>
    <plugins>
    	<plugin>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <groupId>org.codehaus.mojo</groupId>
        <configuration>
          <mainClass>com.profq.app.App</mainClass> <!-- 실행 클래스 -->
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

다음과 같이 설정하고 하기와 같은 goal을 실행하면 해당 클래스가 실행된다.

```shell
$ mvn exec:java
```

다음은 jar 형태로 실행 가능한 패키지를 생성하기 위한 플러그인을 추가할 것이다.

```xml
<!-- 생략 -->
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>com.profq.app.App</mainClass>
      </manifest>
    </archive>
    <descriptorRefs>
      <descriptorRef>jar-with-dependencies</descriptorRef>
    </descriptorRefs>
  </configuration>
</plugin>
<!-- 생략 -->
```

다음과 같이 설정하고 하기와 같은 goal을 실행하면 해당 클래스가 실행된다.

```shell
$ mvn clean compile assembly:single
```

상기 명령은 3가지 goal을 순차적으로 실행하는 것이며 assembly 플러그인은 사전에 컴파일이 수행되어야 정상적으로 패키지 생성이 되기 때문에 추가한 것이다. (clean 골은 반드시 추가할 필요는 없다.)