# Release Notes for REST Assured 5.2.0 #

## Contents
1. [Highlights](#highlights)
1. [Non-backward compatible changes](#non-backward-compatible-changes)
1. [Minor Changes](#minor-changes)

## Highlights
* Much improved CSRF support. For example:
	```
	given().
	       csrf("/users").
	       formParm("firstName", "John").
	       formParm("lastName", "Doe").
	when().
	       post("/users").
	then().
	       statusCode(200);
   ```

  This will first make a `GET` request to `/users` (due to `csrf("/users")`) to get an HTML page that contains the CSRF token.
  Rest Assured will then automatically try to find the input field that contains the CSRF token and include it in the POST to `/users`.
  
  Here's an example of what Rest Assured expects as a response for the GET request to `/users`:

  ```
  <html>
	<head>
	  <title>Add User</title>
	</head>
	<body>
	<form action="/users" method="POST">
	  <table>
	      <tr>
	          <td>First Name:</td>
	          <td><input type="text" name="firstName"></td>
	      </tr>
	      <tr>
	          <td>Last Name:</td>
	          <td><input type="text" name="lastName"></td>
	      </tr>
	      <tr>
	          <td colspan="2"><input name="submit" type="submit"/></td>
	      </tr>
	  </table>
	  <input type="hidden" name="_csrf" value="8adf2ea1-b246-40aa-8e13-a85fb7914341"/>
	</form>
	</body>
  </html>
  ```
  The csrf input field name is called "_csrf", and it'll be automatically detected by REST Assured.
* Fixed so that form authentication takes CSRF into account. The previous form authentication CSRF implementation didn't really work (sorry!). Now you can combine csrf with form authentication and it actually works as expected! Note that for requests other than `GET` or `HEAD`,
  you need to specify _both_ form authentication _and_ csrf, e.g.

  	```
	given().
	       csrf("/users").
	       formParm("firstName", "John").
	       formParm("lastName", "Doe").
	       auth().form("j_spring_security_check", "j_username", "j_password").
	when().
	       post("/users").
	then().
	       statusCode(200);
   ```

   The reason for this is that the server returns a new CSRF token per request. So after the login request (with will use the CSRF token from the login page), REST Assured needs to make an additional `GET` request to `/users` to get a new CSRF token. This token will then finally be supplied with the "POST" request to `/users`.

## Non-backward compatible changes

* Removed the CSRF settings from `io.restassured.authentication.FormAuthConfig` since they didn't work. Use new [CSRF DSL](https://github.com/rest-assured/rest-assured/wiki/Usage#csrf) (described in [highlights](#highlights)) or [io.restassured.config.CsrfConfig](https://github.com/rest-assured/rest-assured/wiki/Usage#csrf-config) to configure CSRF support.

## Minor changes ##
* Support for `PATCH` in multipart uploads
* Upgraded Koltin module to using Kotlin 1.7.10 (previously 1.6.21 was used)
* Improved FilterContext used in Filters by adding the method FilterContext#hasValue(name, object). This makes it easier to check if a value exists _and_ is equal to the expected object.

See [change log](https://github.com/rest-assured/rest-assured/raw/master/changelog.txt) for more details.