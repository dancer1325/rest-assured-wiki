# Contents
1. [Maven / Gradle](#maven--gradle-users)
  1. [REST Assured](#rest-assured)
  1. [JsonPath](#jsonpath)
  1. [XmlPath](#xmlpath)
  1. [JSON Schema Validation](#json-schema-validation)
  1. [Spring Mock MVC](#spring-mock-mvc)
  1. [Scala Module](#scala-support)
1. [Non-maven users](#non-maven-users)
1. [Static Imports](#static-imports)
1. [Version 2.x](#version-2x)
1. [Documentation](#documentation)

## Maven / Gradle Users ##
Add the following dependency to your pom.xml:

### REST Assured ###
Includes [JsonPath](#jsonpath) and [XmlPath](#xmlpath)

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <version>3.0.3</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'io.rest-assured:rest-assured:3.0.3'
```

Notes
  1. You should place rest-assured before the JUnit dependency declaration in your pom.xml / build.gradle in order to make sure that the correct version of Hamcrest is used.
  1. REST Assured includes JsonPath and XmlPath as transitive dependencies

### JsonPath ###
Standalone JsonPath (included if you depend on the `rest-assured` artifact). Makes it easy to parse JSON documents. Note that this JsonPath implementation uses <a href='http://groovy-lang.org/processing-xml.html#_gpath'>Groovy's GPath</a> syntax and is not to be confused with Kalle Stenflo's <a href='https://github.com/json-path/JsonPath'>JsonPath</a> implementation.

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>json-path</artifactId>
      <version>3.0.3</version>
</dependency>
```

Gradle:
```groovy
compile 'io.rest-assured:json-path:3.0.3'
```

### XmlPath ###
Stand-alone XmlPath (included if you depend on the `rest-assured` artifact). Makes it easy to parse XML documents.

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>xml-path</artifactId>
      <version>3.0.3</version>
</dependency>
```

Gradle:
```groovy
compile 'io.rest-assured:xml-path:3.0.3'
```

### JSON Schema Validation ###
If you want to validate that a JSON response conforms to a [Json Schema](http://json-schema.org/) you can use the `json-schema-validator` module:

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>json-schema-validator</artifactId>
      <version>3.0.3</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'io.rest-assured:json-schema-validator:3.0.3'
```

Refer to the [documentation](Usage#json-schema-validation) for more info.

### Spring Mock Mvc ###
If you're using Spring Mvc you can now unit test your controllers using the [RestAssuredMockMvc](http://static.javadoc.io/io.rest-assured/spring-mock-mvc/3.0.3/com/jayway/restassured/module/mockmvc/RestAssuredMockMvc.html) API in the [spring-mock-mvc](https://github.com/jayway/rest-assured/wiki/Usage#spring-mock-mvc-module) module. For this to work you need to depend on the `spring-mock-mvc` module:

Maven:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-mock-mvc</artifactId>
      <version>3.0.3</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'io.rest-assured:spring-mock-mvc:3.0.3'
```

### Scala Support ###
If you're using Scala you may leverage the [scala-support](https://github.com/jayway/rest-assured/wiki/Usage#scala-support-module) module. For this to work you need to depend on the `scala-support` module:

SBT:
```scala
libraryDependencies += "io.rest-assured" % "scala-support" % "3.0.3"
```

Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>scala-support</artifactId>
    <version>3.0.3</version>
    <scope>test</scope>
</dependency>
```

Gradle:
```xml
testCompile 'io.rest-assured:scala-support:3.0.3'
```
## Non-maven users ##
Download [REST Assured](http://dl.bintray.com/johanhaleby/generic/rest-assured-3.0.3-dist.zip) and [Json Schema Validator](http://dl.bintray.com/johanhaleby/generic/json-schema-validator-3.0.3-dist.zip) (optional). You can also download [XmlPath](http://dl.bintray.com/johanhaleby/generic/xml-path-3.0.3-dist.zip) and/or [JsonPath](http://dl.bintray.com/johanhaleby/generic/json-path-3.0.3-dist.zip) separately if you don't need REST Assured. If you're using Spring Mvc then you can download the [spring-mock-mvc](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-3.0.3-dist.zip) module as well. If you're using Scala you may optionally download the [scala-support](http://dl.bintray.com/johanhaleby/generic/scala-support-3.0.3-dist.zip) module. Extract the distribution zip file and put the jar files in your class-path.

# Static imports #

In order to use REST assured effectively it's recommended to statically import methods from the following classes:

```java
io.restassured.RestAssured.*
io.restassured.matcher.RestAssuredMatchers.*
org.hamcrest.Matchers.*
```

If you want to use [Json Schema](http://json-schema.org/) validation you should also statically import these methods:

```java
io.restassured.module.jsv.JsonSchemaValidator.*
```

Refer to [Json Schema Validation](#json-schema-validation) section for more info.

If you're using Spring MVC you can use the [spring-mock-mvc](https://github.com/jayway/rest-assured/wiki/Usage#spring-mock-mvc-module) module to unit test your Spring Controllers using the Rest Assured DSL. To do this statically import the methods from [RestAssuredMockMvc](http://static.javadoc.io/io.rest-assured/spring-mock-mvc/3.0.3/com/jayway/restassured/module/mockmvc/RestAssuredMockMvc.html) _instead_ of importing the methods from `io.rest-assured.RestAssured` and `io.rest-assured.matcher.RestAssuredMatchers`:

```java
io.restassured.module.mockmvc.RestAssuredMockMvc.*
io.restassured.matcher.RestAssuredMatchers.*
```
# Version 2.x #

If you need to depend on an older version replace groupId `io.rest-assured` with `com.jayway.restassured`.

# Documentation #
When you've successfully downloaded and configured REST Assured in your classpath please refer to the [usage guide](Usage) for examples.
