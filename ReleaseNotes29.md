# Release Notes for REST Assured 2.9.0 #

This is a maintenance release but it contains some backward incompatible changes.

# Contents
1. [Non-backward compatible changes](#non-backward-compatible-changes)
1. [Deprecations](#deprecations)
1. [Minor Changes](#minor-changes)

## Non-backward compatible changes ##
* Automatically escapes JsonPath and XmlPath fragments that contains a hyphen and an index lookup operator. For example consider the following JSON document:
  ```javascript
 { some-list : ["one", "two"] }
  ```
  Previously you had to escape `some-list` manually if you wanted to get first element out of the list:
  ```javascript
  JsonPath jsonPath = ...
  String firstElement = jsonPath.getString("'some-list'[0]"); // one
  ```
  Now no explicit escaping is necessary:

  ```javascript
  String firstElement = jsonPath.getString("some-list[0]"); // one
  ```
  But this means that if you previously had a JSON document like this:
  ```javascript
  { "some-list[0]" : ["one", "two"] }
  ```
  you would now have to escape it:
  
  ```javascript
  String firstElement = jsonPath.getString("'some-list[0]'[0]"); // one
  ```
  which makes this an (unlikely but still) non-backward compatible change (issue 564).

## Deprecations
* [FilterContext#getRequestMethod](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/filter/FilterContext.html#getRequestMethod--), use [FilterableRequestSpecification#getMethod](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getMethod--) instead. 
* [FilterContext#getRequestPath](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/filter/FilterContext.html#getRequestPath--), use [FilterableRequestSpecification#getUserDefinedPath](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getUserDefinedPath--) instead.
* [FilterContext#getRequestURI](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/filter/FilterContext.html#getRequestURI--), use [FilterableRequestSpecification#getURI](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getURI--) instead.
* [FilterContext#getOriginalRequestPath](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/filter/FilterContext.html#getOriginalRequestPath--), use [FilterableRequestSpecification#getUserDefinedPath](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getUserDefinedPath--) instead.
* [FilterableRequestSpecification#getRequestContentType](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getRequestContentType--), use [FilterableRequestSpecification#getContentType](http://static.javadoc.io/com.jayway.restassured/rest-assured/2.8.0/com/jayway/restassured/specification/FilterableRequestSpecification.html#getContentType--) instead.


## Minor changes ##
See [change log](http://github.com/jayway/rest-assured/raw/master/changelog.txt) for more details.