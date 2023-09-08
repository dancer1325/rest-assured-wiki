# Spring Support

REST Assured contains two support modules for testing Spring Controllers using the REST Assured API:

* [spring-mock-mvc](#spring-mock-mvc-module) - For unit testing standard Spring [MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html) Controllers
* [spring-web-test-client](#spring-web-test-client-module) - For unit testing (reactive) Spring [Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html) Controllers

## Contents

1. [Spring Mock Mvc Module](#spring-mock-mvc-module)
    1. [Bootstrapping RestAssuredMockMvc](#bootstrapping-restassuredmockmvc)
    1. [Asynchronous Requests](#asynchronous-requests)
    1. [Adding Request Post Processors](#adding-request-post-processors)
    1. [Adding Result Handlers](#adding-result-handlers)
    1. [Using Result Matchers](#using-result-matchers)
    1. [Interceptors](#interceptors)
    1. [Specifications](#specifications)
    1. [Resetting RestAssuredMockMvc](#resetting-restassuredmockmvc)
    1. [Spring MVC Authentication](#spring-mvc-authentication)
        1. [Using Spring Security Test](#using-spring-security-test)
        1. [Injecting a User](#injecting-a-user)
    1. [Kotlin Extension Module for Spring MockMvc](#kotlin-extension-module-for-spring-mockmvc)
1. [Spring Web Test Client Module](#spring-web-test-client-module)
    1. [Bootstrapping RestAssuredWebTestClient](#bootstrapping-restassuredwebtestclient)
    1. [Specifications](#spring-web-test-client-specifications)
    1. [Resetting RestAssuredWebTestClient](#resetting-restassuredwebtestclient)
1. [Common Spring Module Documentation](#common-spring-module-documentation)
    1. [Note on parameters](#note-on-parameters)

## Spring Mock Mvc Module 

REST Assured 2.2.0 introduced support for [Spring Mock Mvc](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/MockMvc.html) using the `spring-mock-mvc` module. This means that you can unit test Spring Mvc Controllers. For example given the following Spring controller:
```java
@Controller
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @RequestMapping(value = "/greeting", method = GET)
    public @ResponseBody Greeting greeting(
            @RequestParam(value="name", required=false, defaultValue="World") String name) {
        return new Greeting(counter.incrementAndGet(), String.format(template, name));
    }
}
```
you can test it using [RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.3.2/io/restassured/module/mockmvc/RestAssuredMockMvc.html) like this:
```java
given().
        standaloneSetup(new GreetingController()).
        param("name", "Johan").
when().
        get("/greeting").
then().
        statusCode(200).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```
i.e. it's very similar to the standard REST Assured syntax. This makes it really fast to run your tests and it's also easier to bootstrap the environment and use mocks (if needed) than standard REST Assured. Most things that you're used to in standard REST Assured works with RestAssured Mock Mvc as well. For example (certain) configuration, static specifications, logging etc etc. To use it you need to depend on the Spring Mock Mvc module:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-mock-mvc</artifactId>
      <version>5.3.2</version>
      <scope>test</scope>
</dependency>
```
Or [download](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-5.3.2-dist.zip) it from the download page if you're not using Maven.

### Bootstrapping RestAssuredMockMvc ##

First of all you should statically import methods in:
```java
io.restassured.module.mockmvc.RestAssuredMockMvc.*
io.restassured.module.mockmvc.matcher.RestAssuredMockMvcMatchers.*
```

instead of those defined in

```java
io.restassured.RestAssured.*
io.restassured.matcher.RestAssuredMatchers.*
```

Refer to [static import](#static-imports) section of the documentation for additional static imports.

In order to start a test using RestAssuredMockMvc you need to initialize it with a either a set of Controllers, a MockMvc instance or a WebApplicationContext from Spring. You can do this for a single request as seen in the previous example:

```java
given().standaloneSetup(new GreetingController()). ..
```
or you can do it statically:

```java
RestAssuredMockMvc.standaloneSetup(new GreetingController());
```
If defined statically you don't have to specify any Controllers (or MockMvc or WebApplicationContext instance) in the DSL. This means that the previous example can be written as:
```java
given().
        param("name", "Johan").
when().
        get("/greeting").
then().
        statusCode(200).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```

### Asynchronous Requests ###
Both RestAssuredMockMvc and  As of version `2.5.0` RestAssuredMockMvc has support for asynchronous requests. For example let's say you have the following controller:
```java
@Controller
public class PostAsyncController {

    @RequestMapping(value = "/stringBody", method = POST)
    public @ResponseBody
    Callable<String> stringBody(final @RequestBody String body) {
        return new Callable<String>() {
            public String call() throws Exception {
                return body;
            }
        };
    }
}
```

You can test this like so:

```java
given().
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

This will use the default timeout of 1 second. You can change the timeout by using the DSL:
```java
given().
        body("a string").
when().
        async().with().timeout(20, TimeUnit.SECONDS).post("/stringBody").
then().
        body(equalTo("a string"));    
```

It's also possible to configure a default timeout by using the [AsyncConfig](http://static.javadoc.io/io.restassured/spring-mock-mvc/2.4.1/io/restassured/module/mockmvc/config/AsyncConfig.html), for example:

```java
given().
        config(config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS))).
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

`withTimeout` is statically imported from `io.restassured.module.mockmvc.config.AsyncConfig` and is just a shortcut for creating an `AsyncConfig` with a given timeout. Apply the config globally to apply to all requests:

```java
RestAssuredMockMvc.config = RestAssuredMockMvc.config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS));

// Request 1
given().
        body("a string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));

// Request 2
given().
        body("another string").
when().
        async().post("/stringBody").
then().
        body(equalTo("a string"));
```

Both request 1 and 2 will now use the default timeout of 100 milliseconds.

### Adding Request Post Processors ###
Spring MockMvc has support for [Request Post Processors](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html) and you can use these in RestAssuredMockMvc as well. For example:
```java
given().postProcessors(myPostProcessor1, myPostProcessor2). ..
```

Note that it's recommended the add `RequestPostProcessors` from `org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors` (i.e. authentication `RequestPostProcessors`) to `auth` instead for better readability (result will be the same):
```java
given().auth().with(httpBasic("username", "password")). ..
```
where httpBasic is statically imported from [SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html).


### Adding Result Handlers ###
Spring MockMvc has support for [Result Handlers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultHandler.html) and you can use these in RestAssuredMockMvc as well. For example let's say you want to use the native MockMvc logging:

```java
.. .then().apply(print()). .. 
```

where `print` is statically imported from `org.springframework.test.web.servlet.result.MockMvcResultHandlers`. Note that if you're using REST Assured 2.6.0 or older you used the `resultHandlers` method:

```java
given().resultHandlers(print()). .. 
```
but this was deprected in REST Assured 2.8.0.

### Using Result Matchers ###
Spring MockMvc provides a bunch of [Result Matchers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultMatcher.html) that you may find useful. RestAssuredMockMvc has support for these as well if needed. For example let's say that for some reason you want to verify that the status code is equal to 200 using a ResultMatcher:
```java
given().
        param("name", "Johan").
when().
        get("/greeting").
then().
        assertThat(status().isOk()).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```
where `status` is statically imported from `org.springframework.test.web.servlet.result.MockMvcResultMatchers`. Note that you can also use the `expect` method which is the same as `assertThat` but more close to the syntax of native MockMvc.

### Interceptors ###
For more advanced use cases you can also get ahold of and modify the [MockHttpServletRequestBuilder](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockHttpServletRequestBuilder.html) before the request is performed. To do this define a [MockHttpServletRequestBuilderInterceptor](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.3.2/io/restassured/module/mockmvc/intercept/MockHttpServletRequestBuilderInterceptor.html) and use it with RestAssuredMockMvc:

```java
given().interceptor(myInterceptor). ..
```

### <a name="specifications"></a> Spring Mock Mvc Specifications ###
Just as with standard Rest Assured you can use [specifications](#specification_re-use) to allow for better re-use. Note that the request specification builder for RestAssuredMockMvc is called [MockMvcRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.3.2/io/restassured/module/mockmvc/specification/MockMvcRequestSpecBuilder.html). The same [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/5.3.2/io/restassured/builder/ResponseSpecBuilder.html) can be used in RestAssuredMockMvc as well though. Specifications can be defined statically as well just as with standard Rest Assured. For example:
```java
RestAssuredMockMvc.requestSpecification = new MockMvcRequestSpecBuilder().addQueryParam("name", "Johan").build();
RestAssuredMockMvc.responseSpecification = new ResponseSpecBuilder().expectStatusCode(200).expectBody("content", equalTo("Hello, Johan!")).build();

given().
        standaloneSetup(new GreetingController()).
when().
        get("/greeting").
then().
        body("id", equalTo(1));
```

### Resetting RestAssuredMockMvc ##
If you've used any static configuration you can easily reset RestAssuredMockMvc to its default state by calling the `RestAssuredMockMvc.reset()` method.

## Spring MVC Authentication ##
Version `2.3.0` of `spring-mock-mvc` supports authentication. For example:
```java
given().auth().principal(..). ..
```
Some authentication methods require Spring Security to be on the classpath (optional). It's also possible to define authentication statically:
```java
RestAssuredMockMvc.authentication = principal("username", "password");
```
where the `principal` method is statically imported from [RestAssuredMockMvc](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.3.2/io/restassured/module/mockmvc/RestAssuredMockMvc.html). It's also possible to define an authentication scheme in a request builder:
```java
MockMvcRequestSpecification spec = new MockMvcRequestSpecBuilder.setAuth(principal("username", "password")).build();
```

### Using Spring Security Test ###

Since version `2.5.0` there's also better support for Spring Security. If you have `spring-security-test` in classpath you can do for example:
```java
given().auth().with(httpBasic("username", "password")). ..
```
where `httpBasic` is statically imported from [SecurityMockMvcRequestPostProcessor](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/request/SecurityMockMvcRequestPostProcessors.html). This will apply basic authentication to the request. For this to work you need apply the [SecurityMockMvcConfigurer](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/test/web/servlet/setup/SecurityMockMvcConfigurers.html) to the MockMvc instance. You can either do this manually:
```java
MockMvc mvc = MockMvcBuilders.webAppContextSetup(context).apply(SecurityMockMvcConfigurers.springSecurity()).build();
```

or RESTAssuredMockMvc will automatically try to apply the `springSecurity` configurer automatically if you initalize it with an instance of [AbstractMockMvcBuilder](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/setup/AbstractMockMvcBuilder.html), for example when configuring a "web app context":
```java
given().webAppContextSetup(context).auth().with(httpBasic("username", "password")). ..
```

Here's a full example:
```java
import io.restassured.module.mockmvc.RestAssuredMockMvc;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.web.context.WebApplicationContext;

import static io.restassured.module.mockmvc.RestAssuredMockMvc.given;
import static org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors.httpBasic;
import static org.springframework.security.test.web.servlet.response.SecurityMockMvcResultMatchers.authenticated;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = MyConfiguration.class)
@WebAppConfiguration
public class BasicAuthExample {

    @Autowired
    private WebApplicationContext context;

    @Before public void
    rest_assured_is_initialized_with_the_web_application_context_before_each_test() {
        RestAssuredMockMvc.webAppContextSetup(context);
    }

    @After public void
    rest_assured_is_reset_after_each_test() {
        RestAssuredMockMvc.reset();
    }

    @Test public void
    basic_auth_example() {
        given().
                auth().with(httpBasic("username", "password")).
        when().
                get("/secured/x").
        then().
                statusCode(200).
                expect(authenticated().withUsername("username"));
    }
}
```

You can also define authentication for all request, for example:
```java
RestAssuredMockMvc.authentication = with(httpBasic("username", "password"));
```
where `with` is statically imported from `io.restassured.module.mockmvc.RestAssuredMockMvc`. It's also possible to use a [request specification](#specifications).

### Injecting a User ###

It's also possible use to of Spring Security test annotations such as [@WithMockUser](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withmockuser) and [@WithUserDetails](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withuserdetails). For example let's say you want to test this controller:

```java
@Controller
public class UserAwareController {

    @RequestMapping(value = "/user-aware", method = GET)
    public
    @ResponseBody
    String userAware(@AuthenticationPrincipal User user) {
        if (user == null || !user.getUsername().equals("authorized_user")) {
            throw new IllegalArgumentException("Not authorized");
        }

        return "Success";
    }
}
```

As you can see the `userAware` method takes a [User](http://docs.spring.io/autorepo/docs/spring-security/current/apidocs/org/springframework/security/core/userdetails/User.html) as argument and we let Spring Security inject it by using the [@AuthenticationPrincipal](http://docs.spring.io/spring-security/site/docs/current/apidocs/org/springframework/security/web/bind/annotation/AuthenticationPrincipal.html) annotation. To generate a test user we could do like this:

```java
@WithMockUser(username = "authorized_user")
@Test public void
spring_security_mock_annotations_example() {
    given().
            webAppContextSetup(context).
     when().
            get("/user-aware").
     then().
            statusCode(200).
            body(equalTo("Success")).
            expect(authenticated().withUsername("authorized_user"));
}
```

### Spring Web Test Client Module 

REST Assured 3.2.0 introduced support for testing components of the [Spring Reactive Web](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html) stack using the `spring-web-test-client` module. This means that you can unit test reactive Spring (Webflux) Controllers. For example let's say that the server defines a controller that returns JSON using this DTO:

```java
public class Greeting {

    private final long id;
    private final String content;

    public Greeting(long id, String content) {
        this.id = id;
        this.content = content;
    }

    public long getId() {
        return id;
    }

    public String getContent() {
        return content;
    }
}
```

The reactive Controller might look like this:  

```java
@RestController
public class GreetingController {

    private static final String template = "Hello, %s!";
    private final AtomicLong counter = new AtomicLong();

    @GetMapping(value = "/greeting", produces = "application/json")
    public Mono<Greeting> greeting(@RequestParam(value="name") String name) {
        return Mono.just(new Greeting(counter.incrementAndGet(), String.format(template, name)));
    }
}
```

you can test it using [RestAssuredWebTestClient](http://static.javadoc.io/io.restassured/spring-web-test-client/5.3.2/io/restassured/module/webtestclient/RestAssuredWebTestClient.html) like this:

```java
package io.restassured.module.webtestclient;

import io.restassured.module.webtestclient.setup.GreetingController;
import org.junit.Test;
import static io.restassured.module.webtestclient.RestAssuredWebTestClient.given;

public class GreetingControllerTest {
    
    @Test 
    public void greeting_controller_returns_json_greeting() {
        given().
                standaloneSetup(new GreetingController()).
                param("name", "Johan").
        when().
                get("/greeting").
        then().
                statusCode(200).
                body("id", equalTo(1)).
                body("content", equalTo("Hello, Johan!"));
    }
}
```
i.e. it's very similar to the standard REST Assured syntax. This makes it really fast to run your tests and it's also easier to bootstrap the environment and use mocks (if needed) than standard REST Assured. Most things that you're used to in standard REST Assured works with RestAssuredWebTestClient as well. For example (certain) configuration, static specifications, logging etc etc. To use it you need to depend on the `spring-web-test-client` module:
```xml
<dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>spring-web-test-client</artifactId>
      <version>5.3.2</version>
      <scope>test</scope>
</dependency>
```
Or [download](http://dl.bintray.com/johanhaleby/generic/spring-web-test-client-5.3.2-dist.zip) it from the download page if you're not using Maven.

### Bootstrapping RestAssuredWebTestClient ##

First of all you should statically import methods in:
```java
io.restassured.module.webtestclient.RestAssuredWebTestClient.*
io.restassured.module.webtestclient.matcher.RestAssuredWebTestClientMatchers.*
```

instead of those defined in

```java
io.restassured.RestAssured.*
io.restassured.matcher.RestAssuredMatchers.*
```

Refer to [static import](#static-imports) section of the documentation for additional static imports.

In order to start a test using RestAssuredWebTestClient you need to initialize it with a either a set of Controllers, a WebTestClient instance or a WebApplicationContext from Spring. You can do this for a single request as seen in the previous example:

```java
given().standaloneSetup(new GreetingController()). ..
```
or you can do it statically:

```java
RestAssuredWebTestClient.standaloneSetup(new GreetingController());
```
If defined statically you don't have to specify any Controllers in the DSL. This means that the previous example can be written as:
```java
given().
        param("name", "Johan").
when().
        get("/greeting").
then().
        statusCode(200).
        body("id", equalTo(1)).
        body("content", equalTo("Hello, Johan!"));  
```

### Spring Web Test Client Specifications ###
Just as with standard Rest Assured you can use [specifications](#specification_re-use) to allow for better re-use. Note that the request specification builder for RestAssuredWebTestClient is called [WebTestClientRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-web-test-client/5.3.2/io/restassured/module/webtestclient/specification/WebTestClientRequestSpecBuilder.html). The same [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/5.3.2/io/restassured/builder/ResponseSpecBuilder.html) can be used in RestAssuredWebTestClient as well though. Specifications can be defined statically as well just as with standard Rest Assured. For example:

```java
RestAssuredWebTestClient.requestSpecification = new WebTestClientRequestSpecBuilder().addQueryParam("name", "Johan").build();
RestAssuredWebTestClient.responseSpecification = new ResponseSpecBuilder().expectStatusCode(200).expectBody("content", equalTo("Hello, Johan!")).build();

given().
        standaloneSetup(new GreetingController()).
when().
        get("/greeting").
then().
        body("id", equalTo(1));
```

### Resetting RestAssuredWebTestClient ##
If you've used any static configuration you can easily reset RestAssuredWebTestClient to its default state by calling the `RestAssuredWebTestClient.reset()` method.

## Common Spring Module Documentation ##

### Note on parameters ###
Neither RestAssuredMockMvc nor RestAssuredWebTestClient differentiates between parameters types, so `param`, `formParam` and `queryParam` currently just delegates to param in MockMvc. `formParam` adds the `application/x-www-form-urlencoded` content-type header automatically though just as standard Rest Assured does.