# Release Notes for REST Assured 3.0.0 #

The next major version of REST Assured

# Contents
1. [Highlights](#highlights)
1. [Other Notable Changes](#other-notable-changes)
1. [Non-backward compatible changes](#non-backward-compatible-changes)
1. [Minor Changes](#minor-changes)

## Highlights
* Now using `java.lang.reflect.Type` instead of `java.lang.Class` in the API for mapping to Java objects. For users of the REST Assured API the change is mostly prominent in the
  [ResponseBodyExtractionOptions](http://static.javadoc.io/io.rest-assured/rest-assured/3.1.0/io/restassured/response/ResponseBodyExtractionOptions.html) interface where the `as` method now takes a `java.lang.reflect.Type` instead of `java.lang.Class`. This should not cause
  any backward incompatibilities unless you've written a custom [ObjectMapperFactory](http://static.javadoc.io/io.rest-assured/rest-assured-common/3.1.0/io/restassured/mapper/factory/ObjectMapperFactory.html). See [non-backward compatible changes](#non-backward-compatible-changes) if this is the case.
* Allow querying (extracting values out of) a request specification without using a filer by using the `io.restassured.specification.SpecificationQuerier`. For example:
 
  ```java
  RequestSpecification spec = ...
  QueryableRequestSpecification queryable = SpecificationQuerier.query(spec);
  String headerValue = queryable.getHeaders().getValue("header");
  String param = queryable.getFormParams().get("someparam");
  ```

## Other Notable Changes ##

* Add better integration for standard HTTP methods with Apache HttpClient which also solves an issue content-type header being generated for empty GET requests
* No longer using `DEF_CONTENT_CHARSET` from Apache HttpClient since it caused compatibility issues
* Removing Authorization header when setting `auth().none()`


## Minor changes ##

See [change log](http://github.com/jayway/rest-assured/raw/master/changelog.txt) for more details.