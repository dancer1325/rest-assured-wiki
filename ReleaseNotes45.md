# Release Notes for REST Assured 4.5.0 #

## Contents
1. [Highlights](#highlights)
1. [Minor Changes](#minor-changes)

## Highlights
* JAXB is now an optional dependency for XmlPath and is now longer required. Depend on 'jakarta.xml.bind:jakarta.xml.bind-api:2.3.3' and `com.sun.xml.bind:jaxb-impl:2.3.3` to re-introduce support for JAXB if you're using Java 11+ (issue [1510](https://github.com/rest-assured/rest-assured/issues/1510) (thanks to Daniel Dyląg for pull request).


## Minor changes ##

See [change log](http://github.com/jayway/rest-assured/raw/master/changelog.txt) for more details.