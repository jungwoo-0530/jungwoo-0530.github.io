# QueryDsl - Can not find Symbol(error)

<https://bgpark.tistory.com/157>를 참고하였습니다.



#### 1. 에러 발생

- 첫 컴파일시에는 QueryDsl이 잘 적용되나 re-compile시 `can not find Symbol` 에러가 등장하였다.



#### 2. 해결 방법

- 



그러나 새로운 에러가 등장하였다.

`Cause: directory not found: /Users/jungwoo/Desktop/git/Web_Study/SecondProject/build/generated/querydsl`







#### 1. Can not find symbol ?

- 컴파일에서 컴파일러가 symbo(선언한 변수 등)을 이해하지 못할 때 발생

#### 2. QeuryDsl는 프로젝트에서 사용하기 위해서는?

http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html

- Gradle환경에서 QueryDsl을 사용하기 위한 2가지 방법

  - `com.ewerk.gradle.plugins.querydsl` 설정
  - `Annotation Processor` 설정

- `com.ewerk.gradle.plugins.querydsl` 설정 방법

  - QueryDsl을 지원하기 위해 나왔던 많은 그레이들 플러그인 중 하나.

  - ```gradle
    //build.gradle
    plugins {
        id "com.ewerk.gradle.plugins.querydsl" version "1.0.10"
    }
    
    def querydslDir = "$buildDir/generated/querydsl"
    querydsl {
        jpa = true
        querydslSourcesDir = querydslDir
    }
    sourceSets {
        main.java.srcDir querydslDir
    }
    configurations {
        querydsl.extendsFrom compileClasspath }
    compileQuerydsl {
        options.annotationProcessorPath = configurations.querydsl
    }
    ```

    





#### 3. generated

- QueryDsl은 컴파일 단계에서 `generated`라는 폴더에 `QClass`를 생성.

- ```gradle
  //build.gradle
  
  def querydslDir = "$buildDir/generated/querydsl"
  querydsl {
      jpa = true
      querydslSourcesDir = querydslDir
  }
  sourceSets {
      main.java.srcDir querydslDir
  }
  configurations {
      querydsl.extendsFrom compileClasspath }
  compileQuerydsl {
      options.annotationProcessorPath = configurations.querydsl
  }
  ```

- 3번째 라인인 `$buildDir/generated/querydsl`가 QClass 생성 위치이다.

- `querydsl` 블록에서 jpa 값을 true를 한다면 자동으로 QClass를 생성한다.

  - 이 때, `com.querydsl.apt.jpa.JPAAnnotationProcessor`가 추가되면서 프로젝트에 적용
  - Annotation processor는 <https://kkambi.tistory.com/84> 링크에 잘 정리가 되어있다.

- 





