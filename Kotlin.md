# Kotlin #

## Contents

1. [Avoid Escaping "when" Keyword](#avoid-escaping-when-keyword)
1. [Kotlin Extension Module](#kotlin-extension-module)
1. [Kotlin Extension Module for Spring MockMvc](#kotlin-extension-module-for-spring-mockmvc)

## Avoid Escaping "when" Keyword

Kotlin is a language developed by [JetBrains](https://www.jetbrains.com/) and it integrates very well with Java and REST Assured. When using it with REST Assured there's one thing that can be a bit annoying. That is you have to escape `when` since it's a reserved keyword in Kotlin. You can do this either by using [Kotlin Extension Module](#kotlin-extension-module) (recommended) or you can simply create your own extension method (the approach shown below). For example:

```kotlin
@Test 
fun `kotlin rest assured example`() {
    given().
            param("firstName", "Johan").
            param("lastName", "Haleby").
    `when`().
            get("/greeting").
    then().
            statusCode(200).
            body("greeting.firstName", equalTo("Johan")).
            body("greeting.lastName", equalTo("Haleby"))
}
```

To get around this, create an [extension function](https://kotlinlang.org/docs/reference/extensions.html) that creates an alias to `when` called `When`:

```kotlin
fun RequestSpecification.When(): RequestSpecification {
    return this.`when`()
}
```

The code can now be written like this:

```kotlin
@Test 
fun `kotlin rest assured example`() {
    given().
            param("firstName", "Johan").
            param("lastName", "Haleby").
    When().
            get("/greeting").
    then().
            statusCode(200).
            body("greeting.firstName", equalTo("Johan")).
            body("greeting.lastName", equalTo("Haleby"))
}
```

Notice that we don't need any escaping anymore. For more details refer to [this](http://code.haleby.se/2015/11/06/rest-assured-with-kotlin/) blog post.

## Kotlin Extension Module

REST Assured 4.1.0 introduced a new module called "kotlin-extensions". This modules provides some useful extension functions when working with REST Assured from Kotlin. First you need to add the module to the project:

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>kotlin-extensions</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>
</dependency>
```

and then import `Given` from the `io.restassured.module.kotlin.extensions` package. You can then use it like this:

```kotlin
val message: String =
Given {
    port(7000)
    header("Header", "Header")
    body("hello")
} When {
    put("/the/path")
} Then {
    statusCode(200)
    body("message", equalTo("Another World"))
} Extract {
    path("message")
}
```

Besides a more pleasing API for Kotlin developers it also has a couple of major benefits to the Java API:
  
1. All failed expectations are reported at the same time
2. Formatting the code in your IDE won't mess up indentation

Note that the names of the extension functions are subject to change in the future (although it's probably not likely). You can read more about the rationale and benefits of the Kotlin API in [this](http://code.haleby.se/2019/09/06/rest-assured-in-kotlin/) blog post.

## Kotlin Extension Module for Spring MockMvc

REST Assured 4.1.0 introduced Kotlin extension support for the [Spring MockMvc](#spring-mock-mvc-module) module. This allows one to write tests like this:
    
```kotlin
class RestAssuredMockMvcKotlinExtensionsTest {

    @Test
    fun example() {
        val mockMvc =
            MockMvcBuilders.standaloneSetup(GreetingController())
                .build()

        val id: Int =
        Given {
            mockMvc(mockMvc)
            param("name", "Johan")
        } When {
            get("/greeting")
        } Then {
            body(
                "id", Matchers.equalTo(1),
                "content", Matchers.equalTo("Hello, Johan!")
            )
        } Extract {
            path("id")
        }

        assertThat(id).isEqualTo(1)
}
```

To use it depend on:

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>spring-mock-mvc-kotlin-extensions</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>
</dependency>
```

and import the extension functions from the `io.restassured.module.mockmvc.kotlin.extensions` package.