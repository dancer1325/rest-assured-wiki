# Contents
1. [Maven / Gradle](#maven--gradle-users)
   1. [REST Assured](#rest-assured)
   1. [JsonPath](#jsonpath)
   1. [XmlPath](#xmlpath)
   1. [JSON Schema Validation](#json-schema-validation)
   1. [Spring Mock MVC](#spring-mock-mvc)
   1. [Spring Web Test Client](#spring-web-test-client)
   1. [Scala Module](#scala-support)
   1. [Kotlin Extensions Module](#kotlin)
   1. [Java 9+](#java-9)
1. [Non-maven users](#non-maven-users)
1. [Static Imports](#static-imports)
1. [Version 2.x](#version-2x)
1. [Documentation](#documentation)

## Maven / Gradle Users ##

### REST Assured ###

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testImplementation 'io.rest-assured:rest-assured:5.5.0'
```

* `rest-assured` dependencies | "pom.xml" or "build.gradle" / ⚠️ before the JUnit dependency declaration ⚠️
  * Reason: 🧠 make sure that correct version of Hamcrest is used 🧠
* 👁️"JsonPath" and "XmlPath" -- are included as -- transitive dependencies, by REST Assured  👁️

### JsonPath ###

* Standalone JsonPath
* included by `rest-assured`
* allows
  * making it easy to parse JSON documents
* JsonPath implementation -- uses -- <a href='http://groovy-lang.org/processing-xml.html#_gpath'>Groovy's GPath</a> syntax
  * != Kalle Stenflo's <a href='https://github.com/json-path/JsonPath'>JsonPath</a> implementation

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>json-path</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testImplementation 'io.rest-assured:json-path:5.5.0'
```

### XmlPath ###

* Stand-alone XmlPath
* included by `rest-assured`
* allows
  * making it easy to parse XML documents

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>xml-path</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testImplementation 'io.rest-assured:xml-path:5.5.0'
```

### JSON Schema Validation ###

* allows
  * validating that a JSON response -- conforms to a -- [Json Schema](http://json-schema.org/)

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>json-schema-validator</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testImplementation 'io.rest-assured:json-schema-validator:5.5.0'
```

* check [documentation](Usage#json-schema-validation)

### Spring Mock Mvc ###
* use case
  * you're using Spring Mvc
* allows
  * unit testing your controllers
* goal
  * [RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.5.0/io/restassured/module/mockmvc/RestAssuredMockMvc.html) API | [spring-mock-mvc](https://github.com/jayway/rest-assured/wiki/Usage#spring-mock-mvc-module) module

    Maven:
    ```xml
    <dependency>
          <groupId>io.rest-assured</groupId>
          <artifactId>spring-mock-mvc</artifactId>
          <version>5.5.0</version>
          <scope>test</scope>
    </dependency>
    ```
    
    Gradle:
    ```groovy
    testImplementation 'io.rest-assured:spring-mock-mvc:5.5.0'
    ```

* if you use Kotlin -> check [documentation](https://github.com/rest-assured/rest-assured/wiki/Usage#kotlin-extension-module-for-spring-mockmvc)
  * == Kotlin extension functions

### Spring Web Test Client ###
* use case
  * you're using Spring Webflux
* allows 
  * unit testing your reactive controllers
* goal
  * [RestAssuredWebTestClient](http://static.javadoc.io/io.restassured/spring-web-test-client/5.5.0/io/restassured/module/webtestclient/RestAssuredWebTestClient.html) API | [spring-mock-mvc](https://github.com/rest-assured/rest-assured/wiki/Usage#spring-mock-mvc-module) module

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-web-test-client</artifactId>
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testImplementation 'io.rest-assured:spring-web-test-client:5.5.0'
```

### Scala Support ###

* use cases
  * you're using Scala

SBT:
```scala
libraryDependencies += "io.rest-assured" % "scala-support" % "5.5.0"
```

Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>scala-support</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

Gradle:
```xml
testImplementation 'io.rest-assured:scala-support:5.5.0'
```

### Kotlin ###

* if you're using Kotlin -> use the [Kotlin Extension Module]( https://github.com/rest-assured/rest-assured/wiki/Usage#kotlin-extension-module)
  * Reason: 🧠provides some useful extension functions 🧠	 

Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>kotlin-extensions</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

Gradle:
```xml
testImplementation 'io.rest-assured:kotlin-extensions:5.5.0'
```

* `Given` -- must be imported from the -- `io.restassured.module.kotlin.extensions` package
* if you're using the [Spring MockMvc module](https://github.com/rest-assured/rest-assured/wiki/Usage#spring-mock-mvc-module) -> [check here](https://github.com/rest-assured/rest-assured/wiki/Usage#kotlin-extension-module-for-spring-mockmvc)

### Java 9 ###

* if you are using Java 9+ & have problems with [split packages](https://www.logicbig.com/tutorials/core-java-tutorial/modules/split-packages.html) -> 👁️ rather than `rest-assured`👁️, use

```xml
<dependency>
   <groupId>io.rest-assured</groupId>
   <artifactId>rest-assured-all</artifactId>
   <version>5.5.0</version>
   <scope>test</scope>
</dependency>
```

## Non-maven users ##

* Download
  * [REST Assured](http://dl.bintray.com/johanhaleby/generic/rest-assured-5.5.0-dist.zip)
  * [Json Schema Validator](http://dl.bintray.com/johanhaleby/generic/json-schema-validator-5.5.0-dist.zip)
    * optional
  * if you don't need REST Assured -> download separately [XmlPath](http://dl.bintray.com/johanhaleby/generic/xml-path-5.5.0-dist.zip) and/or [JsonPath](http://dl.bintray.com/johanhaleby/generic/json-path-5.5.0-dist.zip)
  * if you're using Spring Mvc -> download [spring-mock-mvc](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-5.5.0-dist.zip)
  * if you're using Spring Web Test Client -> download [spring-web-test-client](http://dl.bintray.com/johanhaleby/generic/spring-web-test-client-5.5.0-dist.zip)
  * if you're using Scala -> download [scala-support](http://dl.bintray.com/johanhaleby/generic/scala-support-5.5.0-dist.zip)
  * if you're using Kotlin -> download [kotlin-extensions](http://dl.bintray.com/johanhaleby/generic/kotlin-extensions-5.5.0-dist.zip)
* extract & put the jar | your class-path

# Static imports #

* recommendations
  * statically import methods from

    ```java
    io.restassured.RestAssured.*
    io.restassured.matcher.RestAssuredMatchers.*
    org.hamcrest.Matchers.*
    
    // if you want to use Json Schema validation
    io.restassured.module.jsv.JsonSchemaValidator.*    
    
    // if you are using Spring MVC & want to test Spring Controllers -> import methods from RestAssuredMockMvc
    io.restassured.module.mockmvc.RestAssuredMockMvc.*
    io.restassured.matcher.RestAssuredMatchers.*    
    // NOT from `io.rest-assured.RestAssured` and `io.rest-assured.matcher.RestAssuredMatchers` 
    ```

# Version 2.x #

* if you need older version, replace groupId `io.rest-assured` -- by -> `com.jayway.restassured`

# Documentation #

* requirements
  * REST Assured configured properly | your classpath
* check [usage guide](Usage)
