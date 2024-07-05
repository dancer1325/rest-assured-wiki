# Contents

1. [Scala Extension Module](#scala-extension-module)
1. [Support Module](#scala-support-module)

## Scala Extension Module

REST Assured 5.5.0 introduced a new module called "scala-extensions" for Scala 3. This module provides some useful extension functions when working with REST Assured from Kotlin. First, you need to add the module to the project:

#### SBT:
```scala
libraryDependencies += "io.rest-assured" % "scala-extensions" % "5.5.0"
```

#### Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>scala-extensions</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

#### Gradle:
```xml
testImplementation 'io.rest-assured:scala-extensions:5.5.0'
```

and then import `Given` from the `io.restassured.module.scala.extensions` package. You can then use it like this:

```scala
Given(req =>
      req.port(7000)
      req.header("Header", "Header")
      req.body("hello")
)
.When(
  _.put("/the/path")
)
.Then(res =>
   res.statusCode(200)
   res.body("message", equalTo("Hello World"))
) 
```

## Scala Support Module ##
REST Assured 2.6.0 introduced the [scala-support](http://dl.bintray.com/johanhaleby/generic/scala-support-5.5.0-dist.zip) module that adds an alias to the "then" method defined in the [Response](http://static.javadoc.io/io.rest-assured/rest-assured/5.5.0/io/restassured/response/Response.html) or [MockMvcResponse](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.5.0/io/restassured/module/mockmvc/response/MockMvcResponse.html) called "Then". The reason for this is that `then` might be a reserved keyword in Scala in the future and the compiler issues a warning when using a method with this name. To enable the use of `Then` simply import the `io.restassured.module.scala.RestAssuredSupport.AddThenToResponse` class from the `scala-support` module. For example:

```java
import io.restassured.RestAssured.when
import io.restassured.module.scala.RestAssuredSupport.AddThenToResponse
import org.hamcrest.Matchers.equalTo
import org.junit.Test

@Test
def `trying out rest assured in scala with implicit conversion`() {
  when().
          get("/greetJSON").
  Then().
          statusCode(200).
          body("key", equalTo("value"))
}
```
Note that this is also supported for the [Spring Mock Mvc Module](#spring-mock-mvc-module).

To use it do like this:

#### SBT:
```scala
libraryDependencies += "io.rest-assured" % "scala-support" % "5.5.0"
```

#### Maven:
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>scala-support</artifactId>
    <version>5.5.0</version>
    <scope>test</scope>
</dependency>
```

#### Gradle:
```xml
testImplementation 'io.rest-assured:scala-support:5.5.0'
```

### No build manager:
Download the [distribution file](http://dl.bintray.com/johanhaleby/generic/scala-support-5.5.0-dist.zip) manually.
