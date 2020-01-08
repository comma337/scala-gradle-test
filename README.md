# Intellij에서 Gradle로 Scala 프로젝트 만들기

## 준비 사항
java, gradle는 미리 설치하자.
```
$ java -version
java version "1.8.0_192-ea"
Java(TM) SE Runtime Environment (build 1.8.0_192-ea-b04)
Java HotSpot(TM) 64-Bit Server VM (build 25.192-b04, mixed mode)

$ gradle -v
------------------------------------------------------------
Gradle 6.0.1
------------------------------------------------------------

Build time:   2019-11-18 20:25:01 UTC
Revision:     fad121066a68c4701acd362daf4287a7c309a0f5

Kotlin:       1.3.50
Groovy:       2.5.8
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          1.8.0_192-ea (Oracle Corporation 25.192-b04)
OS:           Mac OS X 10.14.5 x86_64
```

Intellij > Perferences > Scala Plugin 설치하자. (재시작 필요)

![Alt text](README/scala-plugin.png?raw=true "install scala plugin")

## 프로젝트 디렉토리 생성 및 초기화
Intellij의 new project wizard를 통해서도 생성 가능하지만 gradle 명령어로 빠르게 만들 수 있다.
```
$ mkdir scala-gradle-test
$ cd scala-gradle-test
$ gradle init --type scala-library
Select build script DSL:
  1: Groovy
  2: Kotlin
Enter selection (default: Groovy) [1..2] 2

Project name (default: scala-gradle-test):
Source package (default: scala.gradle.test):

> Task :init
Get more help with your project: https://docs.gradle.org/6.0.1/userguide/scala_plugin.html

BUILD SUCCESSFUL in 14s
2 actionable tasks: 2 executed
```

## intellij > Import Project
생성된 프로젝트를 Intellij에서 import 하여 ide 등 몇몇 설정을 추가한다.

![Alt text](README/import-project.png?raw=true "import project")

### build.gradle.kts > repositories 추가
```
repositories {
    jcenter()
    mavenLocal()
    maven { setUrl("http://private.nexus-url.com/nexus/content/groups/public/") }
    mavenCentral()
}
```

### library load 오류 시
로컬 환경에 캐싱된 dependency library가 없는 경우 다음과 같은 오류가 발생한다.

![Alt text](README/resolve-exception.png?raw=true "load lib error")

library를 repository에서 불러오도록 gradlew build 해준다.
```
$ ./gradlew build
<=------------> 7% EXECUTING [7s]
> :compileScala > Resolve files of :compileClasspath > scala-library-2.12.9.jar > 895 KB/5.03 MB downloaded
```
보통 cache된 library 위치는 `~/.gradle/caches/modules-2/files-2.1/<group-id>/<artifact-id>/<version>` 이다.

### Framework Support 설정
자동 생성되는 테스트 파일을 열었을 때 `No Scala SDK in module` 알림이 표시되는 경우 Scala SDK path를 확인하자.

Project > Right click > Add Framework Support > Configure 에서 gradle lib cache path 로 설정하고

![Alt text](README/add-framework-support.png?raw=true "add framework support menu")

![Alt text](README/add-framework-support-error.png?raw=true "add framework support popup")

Gradle window에서 Reimport 해주면 SDK가 적용되어 `No Scala SDK in module` 알림이 사라진다.

![Alt text](README/gradle-reimport.png?raw=true "reimport project")

### 실행 테스트
잘 동작하는지 테스트 코드를 실행해보자.

![Alt text](README/run-test.png?raw=true "run test code")
![Alt text](README/run-test-result.png?raw=true "test code execution result")

## 참고
- https://guides.gradle.org/building-scala-libraries/
