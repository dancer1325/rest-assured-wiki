# Release Notes for REST Assured 2.8.0 #

# Contents
1. [Highlights](#highlights)
1. [Other Notable Changes](#other-notable-changes)
1. [Non-backward compatible changes](#non-backward-compatible-changes)
1. [Deprecations](#deprecations)
1. [Minor Changes](#minor-changes)

## Highlights ##
* Added support for measuring response time. For example:

  ```java
  long timeInMs = get("/lotto").time()
  ```
  
  or using a specific time unit:

  ```java
  long timeInSeconds = get("/lotto").timeIn(SECONDS);
  ```

  where `SECONDS` is just a standard `TimeUnit`. You can also validate it using the validation DSL:

  ```java
  when().
          get("/lotto").
  then().
          time(lessThan(2000L)); // Milliseconds
  ```
  
  or

  ```java
  when().
          get("/lotto").
  then().
          time(lessThan(2L), SECONDS);
  ```

  Please note that response time measurement should be performed when the JVM is hot! (i.e. running a response time measurement when only running a single test will yield erroneous results). This is also implemented in the Spring MockMvc module.
* Lot's of improvements to [filters](https://github.com/jayway/rest-assured/wiki/Usage#filters). 
  * It's now possible to change the request path from a filter, use the [FilterableRequestSpecification#path](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#path-java.lang.String-) method.
  * Added ability to remove parameters from the FilterableRequestSpecification. Use the remove methods such as `removeQueryParam`.
  * Filters can add and remove path parameters as well as getting undefined path parameter placeholders etc (see javadoc for the methods in `com.jayway.restassured.specification.FilterableRequestSpecification`)
* Improvements to path parameters. You can now combine unnamed and name path parameters in the same request. Also the error messages are improved when unnamed path parameters are null.

## Deprecations
* Deprecated [FilterContext#getRequestMethod](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/filter/FilterContext.html#getRequestMethod--) has been deprecated, use [FilterableRequestSpecification#getMethod](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getMethod--) instead. 

## Minor changes ##
See [change log](http://github.com/jayway/rest-assured/raw/master/changelog.txt) for more details.