# Spring Support

* built-in REST Assured modules -- for -- testing Spring Controllers
  * [spring-mock-mvc](#spring-mock-mvc-module)
    * unit testing standard Spring [MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html) Controllers
  * [spring-web-test-client](#spring-web-test-client-module)
    * unit testing (reactive) Spring [Webflux](https://docs.spring.io/spring/docs/current/spring-framework-reference/web-reactive.html) Controllers

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
    1. [Kotlin Extension Module for Spring MockMvc](Kotlin#kotlin-extension-module-for-spring-mockmvc)
1. [Spring Web Test Client Module](#spring-web-test-client-module)
    1. [Bootstrapping RestAssuredWebTestClient](#bootstrapping-restassuredwebtestclient)
    1. [Specifications](#spring-web-test-client-specifications)
    1. [Resetting RestAssuredWebTestClient](#resetting-restassuredwebtestclient)
1. [Common Spring Module Documentation](#common-spring-module-documentation)
    1. [Note on parameters](#note-on-parameters)

## Spring Mock Mvc Module 

* REST Assured v2.2.0+ 
* allows
  * unit testing Spring Mvc Controllers
* syntax == standard REST Assured syntax
* vs standard REST Assured
  * easier to
    * bootstrap the environment
    * use mocks
  * most things work
    * configuration
    * static specification
    * logging
* _Example:_ 

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

    ```java
    given().        // syntax == standard REST Assured syntax
            standaloneSetup(new GreetingController()).
            param("name", "Johan").
    when().
            get("/greeting").
    then().
            statusCode(200).
            body("id", equalTo(1)).
            body("content", equalTo("Hello, Johan!"));  
    ```

* ways to use it
  * add dependency
  
    ```xml
    <dependency>
          <groupId>io.rest-assured</groupId>
          <artifactId>spring-mock-mvc</artifactId>
          <version>5.5.0</version>
          <scope>test</scope>
    </dependency>
    ```
  * [download](http://dl.bintray.com/johanhaleby/generic/spring-mock-mvc-5.5.0-dist.zip) it 

### Bootstrapping RestAssuredMockMvc ##

* statically import module-specific methods
  * check [static import](#static-imports)

      ```java
      io.restassured.module.mockmvc.RestAssuredMockMvc.*
      io.restassured.module.mockmvc.matcher.RestAssuredMockMvcMatchers.*
      // NOT import, standard Rest Assured ones  
      // io.restassured.RestAssured.*
      // io.restassured.matcher.RestAssuredMatchers.*
      ```

* ways to initialize a test -- via -- RestAssuredMockMvc
  * / add 
    * set of Controllers,
    * MockMvc instance
    * WebApplicationContext from Spring
  * scope
    * / 1! request

    ```java
    given().standaloneSetup(new GreetingController())... // set a Controller
    ```
        
    * statically

    ```java
    RestAssuredMockMvc.standaloneSetup(new GreetingController()); // set a Controller
    // -> NOT needed to specify the Controller or MockMvc or WebApplicationContext instance | DSL
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

* RestAssuredMockMvc `v2.5.0+`
* timeout
  * by default, it's 1"
  * configure the timeout
    * `.with().timeout(anyNumber, TimeUnit.SECONDS).`
    * [AsyncConfig](http://static.javadoc.io/io.restassured/spring-mock-mvc/2.4.1/io/restassured/module/mockmvc/config/AsyncConfig.html)
      * `withTimeout`
        * statically imported -- from -- `io.restassured.module.mockmvc.config.AsyncConfig`
        * shortcut for creating an `AsyncConfig` 
* _Example:_

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

    ```java
    given().
            body("a string").
    when().
            async().post("/stringBody").
    then().
            body(equalTo("a string"));
    // configuring the timeOut
    // 1. with().timeout()
    given().
            body("a string").
    when().
            async().with().timeout(20, TimeUnit.SECONDS).post("/stringBody").
    then().
            body(equalTo("a string")); 
    // 2. config().asyncConfig(withTimeout()))
    given().
            config(config().asyncConfig(withTimeout(100, TimeUnit.MILLISECONDS))).
            body("a string").
    when().
            async().post("/stringBody").
    then().
            body(equalTo("a string"));
    ```

    ```java
    // Apply the asyncConfig globally == ALL requests
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

### Adding Request Post Processors ###

* Spring MockMvc provides [Request Post Processors](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html)
  * 👁️can be used | RestAssuredMockMvc 👁️
* _Example:_

    ```java
    given().postProcessors(myPostProcessor1, myPostProcessor2). ..
    ```

* recommendations
  * use `org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors` rather than directly your own implementation
    * _Example:_

    ```java
    import static ...SecurityMockMvcRequestPostProcessor.httpBasic;
    
    given().auth().with(httpBasic("username", "password")). ..
    ```

### Adding Result Handlers ###

* Spring MockMvc provides [Result Handlers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultHandler.html)
  * 👁️can be used | RestAssuredMockMvc 👁️
* _Example:_ use the native MockMvc logging

    ```java
    // required to import statically
    import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;  
    ...then().apply(print())... 
    ```

    ```java
    // if you're using REST Assured v2.6.0- -> you use `resultHandlers()`  
    given().resultHandlers(print()). .. 
    ```

### Using Result Matchers ###

* Spring MockMvc -- provides a bunch of -- [Result Matchers](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/ResultMatcher.html)
  * 👁️can be used | RestAssuredMockMvc 👁️
* _Example:_ verify status code = 200 -- via -- ResultMatcher

    ```java
    import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
    
    given().
            param("name", "Johan").
    when().
            get("/greeting").
    then().
            assertThat(status().isOk()).    // `.expect` can also be used / == MockMvc syntax
            body("id", equalTo(1)).
            body("content", equalTo("Hello, Johan!"));  
    ```

### Interceptors ###

* TODO:
* For more advanced use cases you can also get ahold of and modify the [MockHttpServletRequestBuilder](http://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/web/servlet/request/MockHttpServletRequestBuilder.html) before the request is performed. To do this define a [MockHttpServletRequestBuilderInterceptor](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.5.0/io/restassured/module/mockmvc/intercept/MockHttpServletRequestBuilderInterceptor.html) and use it with RestAssuredMockMvc:

```java
given().interceptor(myInterceptor). ..
```

### <a name="specifications"></a> Spring Mock Mvc Specifications ###

* [specifications](#specification_re-use) can be used | Spring Mock Mvc
  * can be defined statically   
* [MockMvcRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-mock-mvc/5.5.0/io/restassured/module/mockmvc/specification/MockMvcRequestSpecBuilder.html)
  * == request specification builder | RestAssuredMockMvc
* [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/5.5.0/io/restassured/builder/ResponseSpecBuilder.html)
  * can be used | RestAssuredMockMvc
* _Example:_ 

    ```java
    // specifications statically defined
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

* `RestAssuredMockMvc.reset()`
  * reset ANY static configuration -- to -- its default state

## Spring MVC Authentication ##

* requirements
  * REST Asssured `spring-mock-mvc` v`2.3.0+`
*  _Example:_

    ```java
    given().auth().principal(..). ..
    ```
* recommendations
  * add Spring Security | classpath
    * Reason: 🧠Some authentication methods require it 🧠
* ways to define authentication
  * statically

    ```java
      import static io.restassured.springmockmvc....principal;
    
      RestAssuredMockMvc.authentication = principal("username", "password");
    ```
  * authentication scheme | request builder
    
    ```java
      MockMvcRequestSpecification spec = new MockMvcRequestSpecBuilder.setAuth(principal("username", "password")).build();
      ```

### Using Spring Security Test ###

* better support for Spring Security | REST Asssured `spring-mock-mvc` v`2.5.0+`
* recommendations
  * add `spring-security-test` | classpath
* _Example:_

    ```java
    import static ...springsecurity...httpBasic;
    // ways to apply SecurityMockMvcConfigurers.springSecurity() | MockMvc instance         required to apply basic auth afterward 
    // 1. manually
    MockMvc mvc = MockMvcBuilders.webAppContextSetup(context).apply(SecurityMockMvcConfigurers.springSecurity()).build();
    
    // httpBasic        apply basic authentication | request
    given().auth().with(httpBasic("username", "password"))...
  
  
    // or
    // 2. automatically by RESTAssuredMockMvc  -> you need to initialize with an instance of AbstractMockMvcBuilder -- Example: configuring a "web app context"
    given().webAppContextSetup(context).auth().with(httpBasic("username", "password")). ..
    ```

* _Example:_ full example

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

* define authentication | ALL request
    
  ```java
    import static io.restassured.module.mockmvc.RestAssuredMockMvc.with;
    
    RestAssuredMockMvc.authentication = with(httpBasic("username", "password"));
    ```

* check [request specification](#specifications)

### Injecting a User ###

* Spring Security test annotations
  * [@WithMockUser](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withmockuser)
  * [@WithUserDetails](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withuserdetails)
* _Example:_

    ```java
    @Controller
    public class UserAwareController {
    
        @RequestMapping(value = "/user-aware", method = GET)
        public
        @ResponseBody
        String userAware(@AuthenticationPrincipal User user) {
            // @AuthenticationPrincipal     -> user is injected
            if (user == null || !user.getUsername().equals("authorized_user")) {
                throw new IllegalArgumentException("Not authorized");
            }
    
            return "Success";
        }
    }
    ```

    ```java
    @WithMockUser(username = "authorized_user")     // generate a test user
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

* TODO:
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

you can test it using [RestAssuredWebTestClient](http://static.javadoc.io/io.restassured/spring-web-test-client/5.5.0/io/restassured/module/webtestclient/RestAssuredWebTestClient.html) like this:

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
      <version>5.5.0</version>
      <scope>test</scope>
</dependency>
```
Or [download](http://dl.bintray.com/johanhaleby/generic/spring-web-test-client-5.5.0-dist.zip) it from the download page if you're not using Maven.

### Bootstrapping RestAssuredWebTestClient ##

* check [static import](#static-imports)

    ```java
    import static io.restassured.module.webtestclient.RestAssuredWebTestClient.*
    import static io.restassured.module.webtestclient.matcher.RestAssuredWebTestClientMatchers.*
    
    // NOT use
    //io.restassured.RestAssured.*
    //io.restassured.matcher.RestAssuredMatchers.*
    ```
* start a test -- via -- RestAssuredWebTestClient
  * -> needed to initialize it with
    * set of Controllers,
    * `WebTestClient` instance or a `WebApplicationContext` from Spring
  * _Example:_

    ```java
    // 1.    / single request
    given().standaloneSetup(new GreetingController())...
    
    // 2.  statically
    RestAssuredWebTestClient.standaloneSetup(new GreetingController());
    // -> NOT need to specify any Controllers | DSL
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

* [specifications](#specification_re-use) allowed | Spring Web Test Client
  * can be defined statically
* [WebTestClientRequestSpecBuilder](http://static.javadoc.io/io.restassured/spring-web-test-client/5.5.0/io/restassured/module/webtestclient/specification/WebTestClientRequestSpecBuilder.html)
  * == request specification builder | RestAssuredWebTestClient
* [ResponseSpecBuilder](http://static.javadoc.io/io.rest-assured/rest-assured/5.5.0/io/restassured/builder/ResponseSpecBuilder.html)
  * -- can be -- used | RestAssuredWebTestClient
* _Example:_

    ```java
    // define statically
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

* `RestAssuredMockMvc` & `RestAssuredWebTestClient` do NOT differentiate parameters types
  * -> `param`, `formParam` and `queryParam` -- just delegates to -- param | MockMvc
  * `formParam` adds automatically the `application/x-www-form-urlencoded` content-type header == standard Rest Assured does