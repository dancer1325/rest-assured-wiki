# Release Notes for REST Assured 4.5.0 #

## Contents
1. [Highlights](#highlights)
1. [Minor Changes](#minor-changes)

## Highlights
* JAXB is now an optional dependency for XmlPath and is now longer required. Depend on `jakarta.xml.bind:jakarta.xml.bind-api:2.3.3` and `com.sun.xml.bind:jaxb-impl:2.3.3` to re-introduce support for JAXB if you're using Java 11+ (issue [1510](https://github.com/rest-
assured/rest-assured/issues/1510).
* Implemented support for serializing and deserializing XML with Jakarta EE 8 and 9. To use it you need to have Jakarta EE on the classpath, for example by depending on `jakarta.xml.bind:jakarta.xml.bind-api:3.0.1` and `org.glassfish.jaxb:jaxb-runtime:3.0.2`.


## Minor changes ##

See [change log](https://github.com/rest-assured/rest-assured/raw/master/changelog.txt) for more details.