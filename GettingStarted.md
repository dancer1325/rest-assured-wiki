# Contents
1. [Maven / Gradle](#maven--gradle-users)
  1. [REST Assured](#rest-assured)
  1. [JsonPath](#jsonpath)
  1. [XmlPath](#xmlpath)
  1. [JSON Schema Validation](#json-schema-validation)
  1. [Spring Mock MVC](#spring-mock-mvc)
1. [Non-maven users](#non-maven-users)
1. [Static Imports](#static-imports)
1. [Documentation](#documentation)

## Maven / Gradle Users ##
Add the following dependency to your pom.xml:

### REST Assured ###
Includes [JsonPath](#jsonpath) and [XmlPath](#xmlpath)

Maven:
```xml
<dependency>
      <groupId>com.jayway.restassured</groupId>
      <artifactId>rest-assured</artifactId>
      <version>2.6.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'com.jayway.restassured:rest-assured:2.6.0'
```

Notes
  1. You should place rest-assured before the JUnit dependency declaration in your pom.xml / build.gradle in order to make sure that the correct version of Hamcrest is used.
  1. REST Assured includes JsonPath and XmlPath as transitive dependencies

### JsonPath ###
Standalone JsonPath (included if you depend on the `rest-assured` artifact). Makes it easy to parse JSON documents. Note that this JsonPath implementation uses <a href='http://groovy.codehaus.org/GPath'>Groovy's GPath</a> syntax and is not to be confused with Jayway's other <a href='https://github.com/jayway/JsonPath'>JsonPath</a> implementation.

Maven:
```xml
<dependency>
      <groupId>com.jayway.restassured</groupId>
      <artifactId>json-path</artifactId>
      <version>2.6.0</version>
</dependency>
```

Gradle:
```groovy
compile 'com.jayway.restassured:json-path:2.6.0'
```

### XmlPath ###
Stand-alone XmlPath (included if you depend on the `rest-assured` artifact). Makes it easy to parse XML documents.

Maven:
```xml
<dependency>
      <groupId>com.jayway.restassured</groupId>
      <artifactId>xml-path</artifactId>
      <version>2.6.0</version>
</dependency>
```

Gradle:
```groovy
compile 'com.jayway.restassured:xml-path:2.6.0'
```

### JSON Schema Validation ###
If you want to validate that a JSON response conforms to a [Json Schema](http://json-schema.org/) you can use the `json-schema-validator` module:

Maven:
```xml
<dependency>
      <groupId>com.jayway.restassured</groupId>
      <artifactId>json-schema-validator</artifactId>
      <version>2.6.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'com.jayway.restassured:json-schema-validator:2.6.0'
```

Refer to the [documentation](Usage#json-schema-validation) for more info.

### Spring Mock Mvc ###
If you're using Spring Mvc you can now unit test your controllers using the [RestAssuredMockMvc](http://static.javadoc.io/com.jayway.restassured/spring-mock-mvc/2.6.0/com/jayway/restassured/module/mockmvc/RestAssuredMockMvc.html) API in the [spring-mock-mvc](Usage#Spring_Mock_Mvc_Module) module. For this to work you need to depend on the `spring-mock-mvc` module:

Maven:
```xml
<dependency>
      <groupId>com.jayway.restassured</groupId>
      <artifactId>spring-mock-mvc</artifactId>
      <version>2.6.0</version>
      <scope>test</scope>
</dependency>
```

Gradle:
```groovy
testCompile 'com.jayway.restassured:spring-mock-mvc:2.6.0'
```

## Non-maven users ##
Download [REST Assured](http://dl.bintray.com/johanhaleby/generic/rest-assured-2.6.0-dist.zip) and [Json Schema Validator](http://dl.bintray.com/johanhaleby/generic/json-schema-validator-2.6.0-dist.zip) (optional). You can also download [XmlPath](http://dl.bintray.com/johanhaleby/generic/xml-path-2.6.0-dist.zip) and/or [JsonPath](http://dl.bintray.com/johanhaleby/generic/json-path-2.6.0-dist.zip) separately if you don't need REST Assured. If you're using Spring Mvc then you can download the [spring-mock-mvc](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-2.6.0-dist.zip) module as well. Extract the distribution zip file and put the jar files in your class-path.

# Static imports #

In order to use REST assured effectively it's recommended to statically import methods from the following classes:

```java
com.jayway.restassured.RestAssured.*
com.jayway.restassured.matcher.RestAssuredMatchers.*
org.hamcrest.Matchers.*
```

If you want to use [Json Schema](http://json-schema.org/) validation you should also statically import these methods:

```java
com.jayway.restassured.module.jsv.JsonSchemaValidator.*
```

Refer to [Json Schema Validation](#JSON_Schema_validation) section for more info.

If you're using Spring MVC you can use the [spring-mock-mvc](Usage#Spring_Mock_Mvc_Module) module to unit test your Spring Controllers using the Rest Assured DSL. To do this statically import the methods from [RestAssuredMockMvc](http://static.javadoc.io/com.jayway.restassured/spring-mock-mvc/2.6.0/com/jayway/restassured/module/mockmvc/RestAssuredMockMvc.html) _instead_ of importing the methods from `com.jayway.restassured.RestAssured` and `com.jayway.restassured.matcher.RestAssuredMatchers`:

```java
com.jayway.restassured.module.mockmvc.RestAssuredMockMvc.*
com.jayway.restassured.matcher.RestAssuredMatchers.*
```

# Documentation #
When you've successfully downloaded and configured REST Assured in your classpath please refer to the [user guide](Usage) for examples.
