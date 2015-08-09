# Release Notes for REST Assured 2.5.0 #

# Contents
1. [REST Assured](#rest-assured)
  1. [Highlights](#highlights)
  1. [Other Notable Changes](#other-notable-changes)
  1. [Non-backward compatible changes](#non-backward-compatible-changes)
1. [Spring Mock MVC Module](#spring-mock-mvc-module)
  1. [Highlights](#highlights-1)
  1. [Other Notable Changes](#other-notable-changes-1)
  1. [Non-backward compatible changes](#non-backward-compatible-changes-1)
1. [Minor Changes](#minor-changes)

## REST Assured

### Highlights ###
* REST Assured now show all failing body assertions when using multiple expectations in the same body clause. For example:

  ```java
  .. then().body("x.y", equalTo("z"), "y.z", is(2)). ..
  ```
  If both "x.y" and "y.z" fails REST Assured will print both errors. Before only the error of "x.y" was shown.
* Possible to sign the request with an oauth2 access token (in the header) without using Scribe

### Other Notable Changes ###
* It's now possible to set default filename and control name for multiparts. Before they were always equal to "file" but this is now configurable using the new MultiPartConfig. For example:

  ```java
  given().config(config().multiPartConfig(multiPartConfig().with().defaultFileName("custom1").and().defaultControlName("custom2"))). ..
  ```
* Added new a new `NumberReturnType` that can be used with `JsonPathConfig` in order to always return non-integer numbers as doubles. This also you to for example use the `closeTo` Hamcrest matcher. For example:

  ```java
  RestAssured.config = RestAssured.config().jsonConfig(jsonConfig().numberReturnType(DOUBLE));
  ```

* REST Assured can now resolve multiple path parameters inside the same URI "path parameter" (for example `/somewhere/{x}{y}/z`)
* It's now possible to specify how content for a specific content-type should be serialized using `com.jayway.restassured.config.EncoderConfig#encodeContentTypeAs(..)`. For example let's say that you want to serialized content-type `my-custom-content-type` as text:

  ```java
  given().
          config(RestAssured.config().encoderConfig(encoderConfig().encodeContentTypeAs("my-custom-content-type", ContentType.TEXT))).
          contentType("my-custom-content-type").
          content("Some text content").
  when().
          post("/somewhere"). ..
  ```
* Pretty-printing of JSON now displays unicode characters correctly (issue 556)

### Non-backward compatible changes ###


## Spring Mock MVC module

### Highlights ###
* MockMvc module now supports async requests. For example:

  ```java
  given().body(..).when().async().post("/x").then(). ..
  ```
  Big thanks to Marcin Grzejszczak (@mgrzejszczak) for helping out.
* Added support for [RequestPostProcessor](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/request/RequestPostProcessor.html) authentication. For example:
  
  ```java
  given().auth().with(httpBasic("username", "password")). ..
  ```
  where `httpBasic` is statically imported from `org.springframework.security.test.web.servlet.request.SecurityMockMvcRequestPostProcessors`. This requires that you have `spring-security-test` in your classpath. This also means that you can make use of Spring Security test annotations such as [@WithMockUser](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withmockuser) and [@WithUserDetails](http://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#test-method-withuserdetails).
* Added support for RequestPostProcessors to the Spring Mock MVC module, for example:

  ```java
  given().postProcessors(myRequestPostProcessor1, myRequestPostProcessor2). ..
  ```

### Other Notable Changes ###
* It's now possible to set default filename and control name for multiparts. Before they were always equal to "file" but this is now configurable using the new MultiPartConfig. For example:

  ```java
  given().config(config().multiPartConfig(multiPartConfig().with().defaultFileName("custom1").and().defaultControlName("custom2"))). ..
  ```
* It's now possible to supply MockMvcConfigurers when calling webAppContextSetup in the Spring Mock MVC module. For example:

  ```java
  given().webAppContextSetup(context, springSecurity()). ..
  ```
* Added `RestAssuredMockMvc#config()` method that returns the assign static config or a new config if no static config has been assigned
* Spring Mock MVC module automatically registers `org.springframework.security.test.web.servlet.setup.SecurityMockMvcConfigurer` as request post processor if `spring-security-test` is available in classpath and you've supplied an instance of [AbstractMockMvcBuilder](http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/test/web/servlet/setup/AbstractMockMvcBuilder.html) to Rest Assured. This means that you don't have to create a custom MockMvc instance with a `SecurityMockMvcConfigurer` manually which means easier setup. For example if you have `spring-security-test` in classpath it's enough to just do:

  ```java
  given().webAppContextSetup(webAppContext). ..
  ```
  instead of:

  ```java
  given().mockMvc(MockMvcBuilders.webAppContextSetup(context).apply(springSecurity()).build()). ..
  ```
  which you had to do before. You can disable this using the new [MockMvcConfig](http://static.javadoc.io/com.jayway.restassured/spring-mock-mvc/2.5.0/com/jayway/restassured/module/mockmvc/config/MockMvcConfig.html).

### Non-backward compatible changes ###
* The field `RestAssuredMockMvc.mockMvc` has been replaced by the *method* `RestAssuredMockMvc.mockMvc(..)`. Before you did:
 
  ```java
  RestAssuredMockMvc.mockMvc = myMockMvcInstance;
  ```
  but now you need to do:

  ```java
  RestAssuredMockMvc.mockMvc(myMockMvcInstance);
  ```
## Minor changes ##
See [change log](http://github.com/jayway/rest-assured/raw/master/changelog.txt) for more details.