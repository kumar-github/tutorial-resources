# Spring Boot Security 101

Spring Security is a powerful and highly customizable authentication and authorization (access-control) framework. It
also provides protection against common attacks like CSRF etc. It is the de-facto standard for securing Spring-based
applications.
We will explore the fundamentals of Spring Security in a more detailed and practical manner.

## Project Structure

We have a simple Spring Boot project with a rest controller which talks to a service and returns a string response. The
controller contains a bunch of endpoints that are not secured at the moment, but we will secure them using Spring
Security. There is a reason for having these many endpoints (methods). We will apply different security configurations
to each set of endpoints and see how they behave. The idea is as follows:

| S.No. | Endpoint                | HTTP Method | Description                                                                        |
|:-----:|:------------------------|:------------|:-----------------------------------------------------------------------------------|
|   1   | `/unsecured/home`       | `GET`       | **NOT SECURED**. Accessible to everyone irrespective of the authentication status. |
|   2   | `/unsecured/about`      | `GET`       | **NOT SECURED**. Accessible to everyone irrespective of the authentication status. |
|   3   | `/unsecured/create`     | `POST`      | **NOT SECURED**. Accessible to everyone irrespective of the authentication status. |
|       |                         |             |                                                                                    |
|   4   | `/secured/admin`        | `GET`       | **SECURED**. Accessible only to authenticated users with the role `ADMIN`.         |
|   5   | `/secured/user`         | `GET`       | **SECURED**. Accessible only to authenticated users with role `ADMIN` or `USER`.   |
|   6   | `/secured/other`        | `GET`       | **SECURED**. Accessible only to authenticated users with any roles.                |
|   7   | `/secured/admin/create` | `POST`      | **SECURED**. Accessible only to authenticated users with role `ADMIN`.             |
|       |                         |             |                                                                                    |
|   8   | `/api/admin`            | `GET`       | **SECURED**. Accessible only to authenticated users with the role `ADMIN`.         |
|   9   | `/api/user`             | `GET`       | **SECURED**. Accessible only to authenticated users with role `ADMIN` or `USER`.   |
|  10   | `/api/other`            | `GET`       | **SECURED**. Accessible only to authenticated users with any roles.                |
|  11   | `/api/admin/create`     | `POST`      | **SECURED**. Accessible only to authenticated users with role `ADMIN`.             |
|       |                         |             |                                                                                    |
|  12   | `/secured-by-default/1` | `GET`       | **SECURED BY DEFAULT**. Accessible only to authenticated users with any roles.     |
|  13   | `/secured-by-default/2` | `GET`       | **SECURED BY DEFAULT**. Accessible only to authenticated users with any roles.     |
|       |                         |             |                                                                                    |
|  14   | `/deny/1`               | `GET`       | **SECURED**. Access **DENIED** to all users irrespective of roles.                 |
|  15   | `/deny/2`               | `GET`       | **SECURED**. Access **DENIED** to all users irrespective of roles.                 |

The goal is to understand the security configurations indepth and in a step-by-step manner.

## About Tests

At the moment, We have the below unit and integration tests.

1. `SampleServiceUnitTest.java`
2. `SampleControllerUnitTest.java`
3. `SampleControllerMockMvcUnitTest.java`
4. `SampleControllerMockIntegrationTest.java`
5. `SampleControllerServerIntegrationTest.java`

The reason we have these many tests is to understand different testing approaches and the limitations and problems with
those approaches, especially when working with Spring Security.

> [!NOTE]
> Both `SampleControllerUnitTest` and `SampleControllerMockMvcUnitTest` are unit tests but following different
> approaches. The `SampleControllerUnitTest` is a pure unit test without any Spring support where as
> `SampleControllerMockMvcUnitTest` is a unit test with little bit of Spring support.
>
> Since we do not have any Spring support in `SampleControllerUnitTest` we cannot test any Spring MVC specific features
> like security or HTTP status etc. In `SampleControllerMockMvcUnitTest` we can test Spring MVC specific features like
> security, HTTP status, etc.

> [!NOTE]
> Both `SampleControllerMockIntegrationTest` and `SampleControllerServerIntegrationTest` are integration tests but with
> some differences like mock server vs. live server etc. We will see how they behave with Spring Security and how to
> update them to make it work with Spring Security.

### SampleServiceUnitTest

The `SampleServiceUnitTest.java` is a pure unit test with a mocked `HttpServletRequest`. It is pretty straightforward.

### SampleControllerUnitTest

The `SampleControllerUnitTest.java` is also a pure unit test without involving any of the Spring support. It is not
annotated with `@SpringBootTest` or `@WebMvcTest`. So no Spring magic is involved. We are using the
`RestTestClient.bindToController(...)` method which allows us to test specific controller(s) via mock request/response
objects, without running a live server.

In this approach **We** are responsible for creating the controller instance as the Spring container is not set up
during these tests. This also means that if our controller has any dependencies, then **we** need to mock and inject
them ourselves. Since our controller depends on the `SampleService` we are mocking the `SampleService` and injecting it
into the controller.

> [!WARNING]
> The above approach performs pure unit tests without loading the full Spring context. It bypasses all Spring
> infrastructure, such as security configurations, security filters, validations, request/response handling, etc. and
> focuses solely on the controller's logic in isolation. So it does not test any MVC or security-specific behavior.

### SampleControllerMockMvcUnitTest

The `SampleControllerMockMvcUnitTest.java` is also a unit test (slice test) which is annotated with `@WebMvcTest` which
brings in some limited Spring support. `@WebMvcTest` is a specialized annotation used for testing only the web layer of
an application, that focuses only on _Spring MVC_ components. It automatically injects `MockMvc` to simulate HTTP
requests and inspect responses without starting a real live server. It is called slice testing (unit testing
controllers) and not a full integration testing.

When we need to test Spring MVC features like security, we need to bind the `RestTestClient` to a `MockMvc` instance
using the `RestTestClient.bindTo(mockMvc)` method.

By binding the `RestTestClient` to `MockMvc`, the test simulates the full MVC stack without running a real HTTP
server but a mock server. This allows testing our controllers, including security, validation, exception handling, etc.,
while keeping the tests relatively fast.

The easiest way to set up a `MockMvc` in a `@WebMvcTest` is by directly autowiring it like below:

```java
@WebMvcTest(controllers = SampleController.class)
class SampleControllerMockMvcUnitTest {

    @Autowired
    private MockMvc mockMvc;
}
```

We can set up a `MockMvc` in a `@SpringBootTest` like below:

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc
class SampleControllerMockMvcUnitTest {

    @Autowired
    private MockMvc mockMvc;
}
```

> [!NOTE]
> By default, tests that are annotated with `@WebMvcTest` will autoconfigure Spring Security and `MockMvc`. For more
> fine-grained control and customization of `MockMVC` the `@AutoConfigureMockMvc` annotation can be used.

> [!NOTE]
> Typically `@WebMvcTest` is used in combination with `@MockitoBean` or `@Import` to create any collaborators required
> by the `@Controller` beans.

> [!NOTE]
> If you want to load full application context with all configurations and along with `MockMVC`, you should consider
> `@SpringBootTest` combined with `@AutoConfigureMockMvc` rather than `@WebMvcTest` annotation.

> [!NOTE]
> We have used `@MockitoBean` annotation to bring a mocked `SampleService` which is different from the previous
> `Mockito.mock(SampleService.class)` approach.
>
> The `Mockito.mock(SampleService.class)` approach does not work with `@WebMvcTest` while `@MockitoBean` does. It is due
> to how Spring manages the application context in _slice_ tests like `@WebMvcTest`.
>
> `@WebMvcTest` loads a specific _slice_ of the application context, primarily for the web layer. It does not load the
> full application context or automatically scan all service or repository components.
>
> `Mockito.mock()` creates a standalone mock object that is not a Spring bean and is not part of the application
> context. The Spring-managed components (like our controllers) have no way to know about or use this non-Spring mock
> object, so the real dependencies (if found or autoconfigured) are injected. Since we restricted the test to load only
> the `SampleController` using `@WebMvcTest(controllers = SampleController.class)`, the real `SampleService` is not
> loaded and throws a `NoSuchBeanDefinitionException` exception.
>
> `@MockitoBean` tells Spring to add a mock for a specific type to the application context, replacing any existing bean
> of that type or creating a new one if it doesn't exist. This mock is then properly injected into other Spring-managed
> components, like our controller, making it accessible to the test environment.

### SampleControllerMockIntegrationTest

The `SampleControllerMockIntegrationTest.java` is an integration test with mock server (not real http server). It is
annotated with `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)` which brings up the full Spring context but
with a mock server. Since it is just a mock server (no real server started), complete url or port number is not
needed to make requests. It internally uses `MockMvc` to make requests. Since the full application context is loaded,
all the security-related configurations will also be loaded, and we can test against it.

### SampleControllerServerIntegrationTest

The `SampleControllerServerIntegrationTest.java` is a complete integration test similar to
`SampleControllerMockIntegrationTest` but with **real live http server** (_not mock server_) and without any mocks. It
is the same way as clients would consume it over HTTP. It is annotated with `@SpringBootTest(webEnvironment = 
WebEnvironment.RANDOM_PORT)` which brings up the full Spring application context and starts a live server on a random
port. We are using the `RestTestClient.bindToServer()` method with base url and port number to connect to a running live
server to perform a full end-to-end HTTP test. This approach connects to a live server using the real HTTP protocol,
validating full request-response cycles. It is useful for testing CORS, compression, or other network-level behavior
that **cannot** be verified in a web slice tests like `@WebMvcTest` or mock-based test like
`@SpringBootTest(webEnvironment = WebEnvironment.MOCK)`.

## Mockito Extension

The Mockito Extension for JUnit 5 is to simplify the initialization and injection of Mockito mocks in unit tests by
integrating with the JUnit 5 extension model. The primary function of the `MockitoExtension` is to reduce boilerplate
code associated with manual mock creation and injection. Instead of manually creating and injecting mocks using `Mockito.
mock()` the extension automatically initializes fields annotated with `@Mock` and injects them into instances annotated
with `@InjectMocks`.

### @Mock

The `@Mock` annotation in Mockito is a shorthand, convenient, declarative way to create a mock object of a specified
class or interface for use in unit tests. It simplifies code by eliminating the need for repeatedly calling the static
`Mockito.mock()` method.

Using `@Mock` on a field in a test class is equivalent to manually calling `Mockito.mock()` method. This makes the test
code more readable and concise.

### @InjectMocks

The primary advantage of using `@Mock` is its seamless integration with the `@InjectMocks` annotation. `@InjectMocks`
creates a real instance of the class you want to test and automatically injects any fields in that instance that are
annotated with `@Mock`.

When both `@Mock` and `@InjectMocks` are used, Mockito automatically attempts to inject the `@Mock` annotated object
into the fields of the class annotated with `@InjectMocks`.

> [!WARNING]
> The `@ExtendWith(MockitoExtension.class)` annotation is mandatory in order for `@Mock` to work. It tells JUnit to
> register and use the Mockito extension to enable the functionalities of Mockito specific annotations like `@Mock` and
`@InjectMocks` etc.

The below two approaches are exactly the same.

```java
class Demo {

    private final SampleService    mockSampleService = Mockito.mock(SampleService.class);
    private final SampleController sampleController  = new SampleController(mockSampleService);
}
```

```java
@ExtendWith(MockitoExtension.class)
class Demo {

    @Mock
    private SampleService mockSampleService;

    @InjectMocks
    private SampleController sampleController;
}
```

## Introduced Spring Security

To get started with Spring Security, we will add `spring-boot-starter-security` and `spring-boot-starter-security-test`
dependencies to our project. The `spring-boot-starter-security` and `spring-boot-starter-security-test` aggregates
**Spring Security** and **Spring Security Test** related dependencies.

> [!TIP]
> The spring-boot-starter-security-test provides autoconfiguration for testing applications secured with Spring
> Security. It simplifies the process of writing integration and unit tests for secured endpoints by providing essential
> utilities like mocking users and security contexts.

Add the above-mentioned starters to the project and run the application. The following snippet shows some of the output
that indicates that Spring Security is enabled in our application:

```terminaloutput
2026-03-07T11:27:37.018+05:30  WARN 4743 --- [spring-boot-security-101] [           main] .s.a.UserDetailsServiceAutoConfiguration :
Using generated security password: 01920814-b5af-4049-b757-f1c984bc43a6
This generated password is for development use only. Your security configuration must be updated before running your application in production.
2026-03-07T11:27:37.072+05:30  INFO 4743 --- [spring-boot-security-101] [           main] r$InitializeUserDetailsManagerConfigurer : Global AuthenticationManager configured with UserDetailsService bean with name inMemoryUserDetailsManager
2026-03-07T11:27:37.325+05:30  INFO 4743 --- [spring-boot-security-101] [           main] o.s.boot.tomcat.TomcatWebServer          : Tomcat started on port 8080 (http) with context path '/'
2026-03-07T11:27:37.338+05:30  INFO 4743 --- [spring-boot-security-101] [           main] d.b.s.SpringBootSecurity101Application   : Started SpringBootSecurity101Application in 2.335 seconds (process running for 3.23)
```

> [!NOTE]
> If Spring Security is enabled (by adding the `spring-boot-starter-security` dependency), and no explicit security
> related configuration is provided, then a default login and logout pages will be created. An in-memory user with
> username `user` and a random generated password is configured automatically.

### Try hitting an endpoint and see what happens

If we provide the url `http://localhost:8080/unsecured/home` in a browser, it will redirect to a default login page.
Providing `user` as the username and the generated password from the console as password and clicking on the login
button will take you to the respective page or return `/UNSECURED/HOME` in our case. This is called **Form login** or
**Form-based authentication**.

Hitting the same url from _Postman_ or any other non-browser client like curl etc. **without credentials** returns a
`401 Unauthorized` http status code.

Hitting the same url from _Postman_ **with credentials** by selecting `Basic Auth` on the `Authorization` tab. Sending
the request now will return `/UNSECURED/HOME` as response with a `200 OK` http status code. This is called **Http Basic
authentication**.

> [!WARNING]
> Some of our tests are failing now, and we will explore and fix them in the upcoming commits.

Intentionally, we did not make any code changes in this commit except adding Spring Security and Spring Security
Test dependencies and updating this readme file. We are just trying to observe what has happened.
There are many things happened behind the scenes, but the below are the most notable:

1. Spring Security secured all our endpoints automatically and required an **authenticated** user for any endpoint.
2. Spring Security registers a default user with the username `user` and a random generated password at startup that is
   logged to the console.
3. Spring Security created a default login and logout page.
4. The `SampleControllerMockMvcUnitTest` unit test and `SampleControllerMockIntegrationTest` and
   `SampleControllerServerIntegrationTest` integration tests are now failing with the below message:
   ```
   java.lang.AssertionError: Status expected:<200 OK> but was:<401 UNAUTHORIZED>
   Expected :200 OK
   Actual   :401 UNAUTHORIZED
   ```
5. The `SampleServiceUnitTest` and `SampleControllerUnitTest` unit tests are still passing.

> [!NOTE]
> The `SampleServiceUnitTest` and `SampleControllerUnitTest` unit tests are passing, and that makes sense because they
> were just method calls between classes and does not involve any security. We will further explore this when we talk
> about `Method Security`.

> [!WARNING]
> Our tests are failing at the moment, which is not good, and we will fix them in the upcoming commits.

## Overriding the default username and password

In the earlier section we have seen that Spring Security automatically created a default (in-memory) user with the
username `user` and a random generated password at startup. In fact, those are the credentials we used in the login
page and also in _Postman_. It is a good start but not handy as we have to pick the password from the console everytime.

We can override that default username and the random generated password using the below properties in the
`application.properties` file.

```properties
spring.security.user.name     = dev-user-1
spring.security.user.password = dev-password-1
spring.security.user.roles    = USER
```

With the above properties, we can now use `dev-user-1` and `dev-password-1` as the credentials in the login page and in
_Postman_. Try hitting the url `http://localhost:8080/unsecured/home` in the browser and also in _Postman_ with the
new credentials and make sure it works as expected.

> [!NOTE]
> Spring Security now creates an in-memory user same as before but this time with `dev-user-1` as username and
> `dev-password-1` as password with role `USER`. We will talk about roles in the upcoming commits.

> [!WARNING]
> Hardcoding credentials in the `application.properties` file using these properties is useful for _development_ and
> _testing_ purposes but **not recommended for production environments**.

> [!NOTE]
> The above approach gives us a fixed username and password which can be used in the login page and in _Postman_ but
> it does not fix our failing tests. We will fix the tests in the upcoming commits.

## Fixing The Tests

All our tests are failing for the below two reasons:

Tests that are making a `GET` request are failing with the below error:

```terminaloutput
java.lang.AssertionError: Status expected:<200 OK> but was:<401 UNAUTHORIZED>
Expected :200 OK
Actual   :401 UNAUTHORIZED
```

Tests that are making a `POST` request are failing with the below error:

```terminaloutput
java.lang.AssertionError: Status expected:<201 CREATED> but was:<403 FORBIDDEN>
Expected :201 CREATED
Actual   :403 FORBIDDEN
```

### 401 UNAUTHORIZED

It clearly states that we are expecting a `200 OK` but getting a `401 UNAUTHORIZED` http status code.

The tests are making `GET` requests to specified urls the same way as we do in the browser or in _Postman_ or _curl_ but
without providing any credentials. If no credentials are provided, then there is no authenticated user present. But
Spring Security expects the endpoints should be accessed only by authenticated users (unless we have configured it to
allow anonymous access, which we did not). So it returns a `401 UNAUTHORIZED` http status code which does not match our
test's expectation.

There should be a way to simulate an authenticated user in our tests without involving any real users or credentials.

### 403 FORBIDDEN

It clearly states that we are expecting a `201 CREATED` but getting a `403 FORBIDDEN` http status code.

The tests are making `POST` requests to specified urls the same way as we do in _Postman_ or _curl_ but without
providing any credentials. The `403 FORBIDDEN` is happening only to the `POST` requests and even if we provide valid
credentials, it still returns a `403 FORBIDDEN` http status code. Spring Security expects something called a `CSRF`
token for all state changing requests (like `POST`, `PUT`, `PATCH`, `DELETE`) and we are not providing it in our tests.
We will talk about `CSRF` tokens in the upcoming sections and for now we will just ignore the failing tests.

### Fixing Unit Tests

The `SampleServiceUnitTest` and `SampleControllerUnitTest` tests does not have any failures as they are direct method
calls and nothing to do with security.

The `SampleControllerMockMvcUnitTest` test is also a unit test and do not start a real server but create a mock server
using `MockMvc`, so we have quite a few options to simulate an authenticated user.

#### @WithMockUser

`@WithMockUser` is a Spring Security annotation used in testing to simulate a user (an authenticated user) in a test
environment without needing an actual authentication mechanism like a real login form or token. It is often used with
`MockMvc` (`@WebMvcTest`, `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)`) to test secured endpoints in MVC
controllers. It is a part of the `spring-boot-starter-security-test` dependency.

It injects a `SecurityContext` containing a `UsernamePasswordAuthenticationToken` with specified username, password, and
roles.

Basically, `@WithMockUser` allows us to run tests as a specific (mocked) user.
By default, `@WithMockUser` creates a user with username `user`, password `password`, and role `USER` (internally stored
as `ROLE_USER`).

By annotating a test class with `@WithMockUser`, all the tests in that class will run as a user with the username
`user`, password `password`, and role `ROLE_USER`.

> [!NOTE]
> The user with a username of `user` does not have to exist, instead a mock user object will be created.
> The mock user object has a username of `user`.
> The mock user object has a password of `password`.
> The mock user object has a single role as `ROLE_USER`.

The simple `@WithMockUser` variant is handy, because it lets us use a lot of defaults (default username,
password, roles). But we can configure `@WithMockUser` with different username, password, and roles as below:

`@WithMockUser(username = "john", password = "johnpassword" roles = "USER")`

The above will run a test with `john` as username, `johnpassword` as password with `USER` role.

> [!NOTE]
> You may be wondering how the tests are passing if `@WithMockUser` creates a user with username `user` and password
> `password` and role `ROLE_USER` which does not match our actual credentials (`dev-user-1` and `dev-password-1`). The
> reason is that these tests (only `MockMvc` tests) are run against a mock server (not a real server) and that mock
> server just needs an authenticated user object in the `SecurityContext`. It does not need to be an actual user with
> the same credentials.

> [!TIP]
> `@WithMockUser` can be used at both class and method level. If used at class level, the same credentials will be
> used for all the tests in that class. If used both at class and method level, the method level credentials will
> override the class level credentials for that specific test method.
>
> It is often used at class level to set the default credentials for all the tests in that class and at method level for
> specific tests to override the default credentials.

> [!WARNING]
> As mentioned above, `@WithMockUser` is only for tests that are using `MockMvc` like `@WebMvcTest`,
> `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)` but not for tests that starts a real server like
> `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`. So `@WithMockUser` will not work on our
> `SampleControllerServerIntegrationTest` tests. Since `SampleControllerServerIntegrationTest` starts a real server, we
> need real authentication with real credentials.

### Fixing Integration Tests

Integration tests start a real live server instead of a mock server, we cannot mock authentication instead pass real
credentials. So we have very limited options.

1. We can have a custom security configuration for our integration tests that skips the authentication and allows
   anonymous access.
2. We can pass real credentials.

We will explore custom security configuration in the upcoming commits, but for now let's focus on the second option.
To fix the `SampleControllerServerIntegrationTest` we will pass the real credentials via http headers. We can do
that once for all requests or for individual requests.

If we want to set the same credentials for all requests, then we can do that while constructing the `RestTestClient`
object as below:

```java
RestTestClient restClient = RestTestClient.bindToServer()
                                          .baseUrl("http://localhost:" + port)
                                          .defaultHeaders(
                                                  headers -> headers.setBasicAuth("dev-user-1", "dev-password-1"))
                                          .build();
```

If we want to set different credentials for different requests, then we need to do it on individual requests as below:

```java
RestTestClient restClient = restClient.get()
                                      .uri(uri)
                                      .headers(headers -> headers.setBasicAuth("dev-user-1", "dev-password-1"))
                                      .exchange()
                                      .expectStatus()
                                      .isOk()
                                      .expectBody(String.class)
                                      .isEqualTo(expected);
```

> [!TIP]
> We can also set default headers for all requests and override them on individual requests too.

> [!WARNING]
> Tests that are making `POST` requests are still failing. We will fix them after we talk about custom security
> configurations and `CSRF` tokens in the upcoming sections.

## Test Specific Credentials

If we do not want to use the credentials from the `application.properties` file and want to have credentials specific
to tests then we can use the `@TestPropertySource` annotation and define the credentials in the test class itself.
These credentials are specific to that test class and will be used for all the tests in that class. We can further
move this to a `@TestConfiguration` class and use it all the tests using `@Import` annotation.

> [!NOTE]
> The above approach is nothing specific to Spring Security, but it is a common practice where we want to override
> properties like database url, username, password, server port, etc. only for tests.

We overrode the default username and password in the `application.properties` with the test-specific credentials
defined in the `SampleControllerServerIntegrationTest` class. `SampleControllerServerIntegrationTest` will now use
`test-user` and `test-password` as credentials instead of `dev-user-1` and `dev-password-1`.

## @WithAnonymousUser

While `@WithMockUser` is used to run tests as an authenticated user, `@WithAnonymousUser` in contrast allows tests to
run as an anonymous user. This is especially useful when you want to run most tests with an authenticated user but few
tests as an anonymous (unauthenticated) user where authentication is not needed to access some endpoints.
Also, useful when testing negative scenarios like accessing a secured endpoint without authentication should return
`401 Unauthorized`.

| Annotation           | Purpose                                    | Usage Context                                              |
|----------------------|--------------------------------------------|------------------------------------------------------------|
| `@WithMockUser`      | Simulates an authenticated user for tests. | When you need to test methods requiring authentication.    |
| `@WithAnonymousUser` | Simulates an anonymous user for tests.     | When you want to run methods not requiring authentication. |

In `SampleControllerMockMvcUnitTest` and `SampleControllerMockIntegrationTest` tests we have used `@WithMockUser` at
class level to run all tests with an authenticated user. Later we are overriding that with `@WithAnonymousUser` at
method level in some tests to run the test as an anonymous (unauthenticated) user and make sure that the endpoint is not
accessible without authentication and returns `401 Unauthorized`.

In `SampleControllerServerIntegrationTest` test we have removed the basic authentication default header (by overriding
at method level) from the `RestTestClient` only for a specific test method and run the test as an unauthenticated user
and make sure that the endpoint is not accessible without authentication and returns `401 Unauthorized`.

## Terminology (Authenticated, Unauthenticated, Anonymous)

Before entering into the details of the Authentication, let's have some clear understanding on Spring's terminology
about Authentication.

| Status                                | What it means                                                                                                                                                                                            |
|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Authenticated                         | The user's credentials have been validated and accepted. The framework **knows** who the user is.                                                                                                        |
| Unauthenticated                       | The user never entered the credentials and so was not authenticated. The framework **does not know** who the user is.                                                                                    |
| Anonymous (Anonymously Authenticated) | The user never entered the credentials, but the framework has authenticated the user by automatically creating an anonymous user with some default information. The framework **knows** who the user is. |

The difference between Unauthenticated and Anonymous is very subtle. In fact, there is no real conceptual difference
between an _Unauthenticated user_ and an _Anonymously Authenticated user_.

But technically, An Unauthenticated user contains null (No `Authentication` object) in the `SecurityContextHolder` while
an Anonymous (Anonymously Authenticated) user contains a special `AnonymousAuthenticationToken`. The
`AnonymousAuthenticationFilter` automatically inserts this token if no other authentication has occurred, allowing all
users (including anonymous ones) to have an `Authentication` object in the security context.

Spring Security’s Anonymous Authentication just gives a more convenient way to configure access-control attributes.
There are other situations where anonymous authentication is useful, such as when an auditing interceptor queries the
`SecurityContextHolder` to identify which principal was responsible for a given operation. Classes can be authored more
robustly if they know the `SecurityContextHolder` always contains an `Authentication` object and never contains null.

We will see more about Authentication and Anonymous Authentication in the upcoming sections.

> [!NOTE]
> When Anonymous Authentication is disabled, if an Unauthenticated user accesses a secured resource without credentials
> will return a strict `401 Unauthorized` response whereas in Anonymous user, we have a convenient way to apply
> access-control rules to all requests by giving them a default authority like `ROLE_ANONYMOUS`.

## Filter and FilterChain

Now that we have known a little bit of Spring Security, We need to dive deeper into the internals of Spring Security.
How Spring Security is able to intercept all the incoming requests and force authentication before they reach the
target controller. To do that, We need to understand the concept of **Filters** and **FilterChain** which are the basic
building blocks of Spring Security.

In a web application, any HTTP request sent by the client is, by default, passed directly to the target servlet. The
response that the target servlet generates is, by default, passed directly back to the client with its content
unmodified. Here the servlet is processing the request and generating the response that the application requires.

But there are cases where some pre-processing of the request and some post-processing of the response would be needed.
If the pre-processing and post-processing are needed only for a single servlet, then we can do it directly in the
respective servlet itself. But what if the same kind of pre-processing and post-processing is needed in multiple
servlets?

Filters and FilterChains are the way to achieve this.

### Filter

A **Filter** is a Java object that can:

* intercept requests coming from a client before it reaches the target servlet.
* intercept responses coming from a servlet before it reaches the client.
* use and/or modify the information contained in the requests before it reaches the target servlet.
* use and/or modify the information contained in the response after they are generated but before it is sent to the
  client.

Basically filters are used for _pre-processing_ _requests_ and _post-processing responses_.

Typical filters include:

* Logging filter.
* Authentication filter.
* Encryption filter.
* Compression filter.

### FilterChain

A **FilterChain** as the name suggests, is a chain of filters. It is an ordered collection of independent filters.
Each filter has its own responsibility and **FilterChain** coordinates their processing. It provides a mechanism for
passing a request and response to the next filter in the chain.

Filters can be chained together so that a group of filters can act on the input and output of a specified resource or
group of resources.

Let's say we want to _log_ all the requests coming from the client and _look for a specific HTTP header_ in the request
before it reaches the target servlet (Pre Processing). If the header is not found, then we want to return a
`400 Bad Request` http status code and terminate the request from proceeding further. If the header is found, then we
want to continue with the request processing. Once the request is processed by the target servlet, we want to _add a
specific HTTP header_, _encrypt_ and _compress_ the response before it is sent to the client (Post Processing).

A typical implementation of the above-mentioned scenario using filters and filter chain would be as follows:

1. The client sends a request.
2. The servlet container determines the applicable `Filter`s and creates a `FilterChain`.
3. The servlet container invokes the `doFilter()` method of the first filter in the chain. In our case, it is the
   `LoggingFilter`.
4. Within the `LoggingFilter`'s `doFilter()` method, any code before `filterChain.doFilter()` is executed. In our case,
   we are just logging the request. This is Pre Processing.
5. `filterChain.doFilter()` in `LoggingFilter` calls the next filter in the filter chain. In our case, it is
   the `HeaderCheckFilter`.
6. Within the `HeaderCheckFilter`'s `doFilter()` method, any code before `filterChain.doFilter()` is executed. In our
   case, we are checking if the request contains a specific header. If the **header is not found**, we return a
   `400 Bad Request` http status code and terminate the request. If the **header is found**, we continue with the
   request processing. This is Pre Processing.
7. Next `CompressionFilter`'s `doFilter()` method is called but there is nothing to pre-process.
8. Next `EncryptionFilter`'s `doFilter()` method is called but there is nothing to pre-process.
9. Next `HeaderAddFilter`'s `doFilter()` method is called but there is nothing to pre-process.
10. Next the request is passed to the target servlet as there is no filter in the chain. In our case, it is the
    `FilterDemoController`.
11. The `FilterDemoController` processes the request and generates the response.
12. Next the `HeaderAddFilter`'s `doFilter()` method is called with the response. In our case, we are adding a
    specific header to the response. This is Post Processing.
13. Next the `EncryptionFilter`'s `doFilter()` method is called with the response. In our case, we are just logging
    the response as encrypted. This is Post Processing.
14. Next the `CompressionFilter`'s `doFilter()` method is called with the response. In our case, we are checking if the
    response contains a specific header which was added by the `HeaderAddFilter` and log a message depends on the
    header is present or not. This is Post Processing.
15. Next, the final post-processed response is sent back to the client.

Check the below sequence diagram for a better understanding of the above-mentioned scenario.

<p align="center">
<img alt="filters-sequence-diagram" src="https://github.com/user-attachments/assets/ad6677b4-a174-4cec-b6f7-75b0ac2ae0d3">
</p>

Sample code for the above scenario has been implemented for reference. Run the code and hit the url
`http://localhost:8080/hello` in _Postman_ or using _curl_ with and without the `X-Api-Version` header to see how
different filters are working.

> [!WARNING]
> Do not worry about the `401 Unauthorized` http status code as we have Spring Security in place. What we are interested
> in here is the log messages in the console and the response headers.

> [!NOTE]
> The filters are configured to intercept only the requests with `/hello` path and nothing else. This is done to prevent
> our `SampleController` from being intercepted by the filters and that will make the tests fail.

Request without the `X-Api-Version` header:

```bash
curl http://localhost:8080/hello
```

Console log:

```terminaloutput
2026-03-12T14:42:13.958+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-7] d.b.s.filters.LoggingFilter       : Request received : /hello
2026-03-12T14:42:13.959+05:30 ERROR 21837 --- [spring-boot-security-101] [nio-8080-exec-7] d.b.s.filters.HeaderCheckFilter   : Required header missing. Request aborted.
```

Response with headers:

```bash
HTTP/1.1 401
Set-Cookie: JSESSIONID=B6AE161F889EDEB5072EF11B4F4F9A76; Path=/; HttpOnly
WWW-Authenticate: Basic realm="Realm"
X-Content-Type-Options: nosniff
X-XSS-Protection: 0
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Length: 0
Date: Thu, 12 Mar 2026 09:13:21 GMT
```

Request with `X-Api-Version` header:

```bash
curl -i -H "X-Api-Version: 1.1" http://localhost:8080/hello
```

Console log:

```terminaloutput
2026-03-12T14:42:48.391+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-9] d.b.s.filters.LoggingFilter       : Request received : /hello
2026-03-12T14:42:48.391+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-9] d.b.s.filters.HeaderCheckFilter   : Required header found. Request will proceed.
2026-03-12T14:42:48.391+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-9] d.b.s.filters.HeaderAddFilter     : Header added to response.
2026-03-12T14:42:48.394+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-9] d.b.s.filters.EncryptionFilter    : Response Encrypted.
2026-03-12T14:42:48.394+05:30  INFO 21837 --- [spring-boot-security-101] [nio-8080-exec-9] d.b.s.filters.CompressionFilter   : Header found. Response Compressed.
```

Response with headers:

```bash
HTTP/1.1 401
X-Allow-Compression: true
Set-Cookie: JSESSIONID=E63B9A3A903E3D705A440A29C67AA858; Path=/; HttpOnly
WWW-Authenticate: Basic realm="Realm"
X-Content-Type-Options: nosniff
X-XSS-Protection: 0
Cache-Control: no-cache, no-store, max-age=0, must-revalidate
Pragma: no-cache
Expires: 0
X-Frame-Options: DENY
Content-Length: 0
Date: Thu, 12 Mar 2026 09:12:48 GMT
```

Request with `X-Api-Version` header and authentication:

```bash
curl -i -H "X-Api-Version: 1.1" -u "dev-user-1:dev-password-1" http://localhost:8080/hello
```

```terminaloutput
2026-03-12T15:13:21.797+05:30  INFO 28344 --- [spring-boot-security-101] [nio-8080-exec-8] d.b.s.filters.LoggingFilter       : Request received : /hello
2026-03-12T15:13:21.797+05:30  INFO 28344 --- [spring-boot-security-101] [nio-8080-exec-8] d.b.s.filters.HeaderCheckFilter   : Required header found. Request will proceed.
2026-03-12T15:13:21.797+05:30  INFO 28344 --- [spring-boot-security-101] [nio-8080-exec-8] d.b.s.filters.HeaderAddFilter     : Header added to response.
2026-03-12T15:13:21.912+05:30  INFO 28344 --- [spring-boot-security-101] [nio-8080-exec-8] d.b.s.filters.EncryptionFilter    : Response Encrypted.
2026-03-12T15:13:21.912+05:30  INFO 28344 --- [spring-boot-security-101] [nio-8080-exec-8] d.b.s.filters.CompressionFilter   : Header found. Response Compressed.
```

Response with headers:

```bash
HTTP/1.1 200
X-Allow-Compression: true
Content-Type: text/plain;charset=UTF-8
Content-Length: 5
Date: Thu, 12 Mar 2026 09:36:42 GMT

Hello%
```

## Spring Security Architecture

In the previous section, we have seen how filters and filter chains work together to achieve pre-processing and
post-processing of requests and responses.

Spring Security is completely based on Servlet `Filter`s and `FilterChain`s, and it is important to understand how they
work. If the previews section was not clear enough, then let's break it down a little bit.

The following image shows the typical layering of the handlers for a single HTTP request.

<p align="center">
<img width="500px" alt="filter-chain" src="https://github.com/user-attachments/assets/494f8ca4-c647-4273-b8ae-5fc6dc704b3c">
</p>

The client sends a request to the application, and the servlet container creates a `FilterChain`, which contains one
or more `Filter` instances, and a `Servlet` that should process the `HttpServletRequest`, based on the path of the
request URI. In a Spring MVC application, the `Servlet` is an instance of `DispatcherServlet`. At most, one `Servlet`
can handle a single `HttpServletRequest` and `HttpServletResponse`. However, more than one `Filter` can be used to:

1. Prevent downstream `Filter` instances or the `Servlet` from being invoked. In this case, typically the `Filter`
   itself writes the `HttpServletResponse`.
2. Modify the `HttpServletRequest` or `HttpServletResponse`, which is then used by the downstream `Filter` instances and
   the `Servlet`.

The power of the `Filter`s comes from the `FilterChain` that is passed into it.

```java
@Override
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws
                                                                                          IOException,
                                                                                          ServletException {
    // do something before the rest of the application
    chain.doFilter(request, response); // invoke the rest of the application
    // do something after the rest of the application
}
```

> [!TIP]
> Since a `Filter` impacts only downstream `Filter` instances and the `Servlet`, the order in which each `Filter` is
> invoked is extremely important.

### DelegatingFilterProxy

The Servlet container allows registering `Filter` instances by using its own standards, but it is not aware of
Spring-defined Beans. Spring provides a `Filter` implementation named `DelegatingFilterProxy` that allows bridging
between the Servlet container’s lifecycle and Spring’s `ApplicationContext`. We can register `DelegatingFilterProxy`
through the standard Servlet container mechanisms but delegate all the work to a Spring Bean that implements `Filter`.

Here is a picture of how `DelegatingFilterProxy` fits into the `Filter` instances and the `FilterChain`.

<p align="center">
<img width="500px" alt="delegating-filter-proxy" src="https://github.com/user-attachments/assets/a6ebf966-8dc1-4d97-a8ed-1d3654c6671f">
</p>

`DelegatingFilterProxy` looks up `Bean Filter-1` from the `ApplicationContext` and then invokes `Bean Filter-1`. The
following listing shows pseudocode of `DelegatingFilterProxy`:

```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    // 1
    Filter delegate = getFilterBean(someBeanName);
    // 2
    delegate.doFilter(request, response);
}
```

1. The `getFilterBean(...)` lazily get `Filter` that was registered as a Spring Bean. For example, in
   `DelegatingFilterProxy` `delegate` is an instance of `Bean Filter-1`.
2. `delegate.doFilter(...)` delegate work to the Spring Bean.

> [!NOTE]
> Another benefit of `DelegatingFilterProxy` is that it allows delaying looking up `Filter` bean instances. This is
> important because the servlet container needs to register the `Filter` instances before the servlet container can
> start up. However, Spring typically uses a `ContextLoaderListener` to load the Spring Beans, which is not done until
> after the `Filter` instances need to be registered.

### FilterChainProxy

Spring Security’s Servlet support is contained within `FilterChainProxy`. `FilterChainProxy` is a special `Filter`
provided by Spring Security that allows delegating to many `Filter` instances through `SecurityFilterChain`. Since
`FilterChainProxy` is a `Bean`, it is typically wrapped in a `DelegatingFilterProxy`.

The following image shows the role of `FilterChainProxy`.

<p align="center">
<img width="800px" alt="filter-chain-proxy" src="https://github.com/user-attachments/assets/e9534b09-bce7-452b-b7a1-bb8426368152">
</p>

### SecurityFilterChain

`SecurityFilterChain` is used by `FilterChainProxy` to determine which Spring Security `Filter` instances should be
invoked for the current request.

The following image shows the role of `SecurityFilterChain`.

<p align="center">
<img width="800px" alt="security-filter-chain" src="https://github.com/user-attachments/assets/d4e342dd-bc45-4442-8e54-089faaef1b59">
</p>

The Security Filters in `SecurityFilterChain` are typically Beans, but they are registered with `FilterChainProxy`
instead of `DelegatingFilterProxy`. `FilterChainProxy` provides a number of advantages to registering directly with the
Servlet container or `DelegatingFilterProxy`.

First, it provides a starting point for all of Spring Security’s Servlet support.

Second, since `FilterChainProxy` is central to Spring Security usage, it can perform tasks that are not viewed as
optional. For example, it clears out the `SecurityContext` to avoid memory leaks. It also applies Spring Security’s
HttpFirewall to protect applications against certain types of attacks.

In addition, it provides more flexibility in determining when a `SecurityFilterChain` should be invoked. In a Servlet
container, `Filter` instances are invoked based upon the URL alone. However, `FilterChainProxy` can determine invocation
based upon anything in the `HttpServletRequest` by using the `RequestMatcher` interface.

> [!TIP]
> Since `FilterChainProxy` is the starting point for all of Spring Security's Servlet support, if you ever try to
> troubleshoot anything related to Spring Security, adding a debug point in `FilterChainProxy` is a great place to
> start.

The following image shows multiple `SecurityFilterChain` instances:

<p align="center">
<img width="1000px" alt="security-filter-chains" src="https://github.com/user-attachments/assets/3fd31eae-89d1-47c3-a958-d3519309eec7">
</p>

In the multiple security filter chain figure, `FilterChainProxy` decides which `SecurityFilterChain` should be used.
Only the first `SecurityFilterChain` that matches is invoked. If a URL of `/api/messages/` is requested, it first
matches on the `SecurityFilterChain-1` pattern of `/api/**`, so only `SecurityFilterChain-1` is invoked, even though it
also matches on `SecurityFilterChain-N`. If a URL of `/messages/` is requested, it does not match on the
`SecurityFilterChain-1` pattern of `/api/**`, so `FilterChainProxy` continues trying each `SecurityFilterChain`.
Assuming that no other `SecurityFilterChain` instances match, `SecurityFilterChain-N` is invoked.

Notice that `SecurityFilterChain-1` has only three security `Filter` instances configured. However,
`SecurityFilterChain-N` has four security `Filter` instances configured. It is important to note that each
`SecurityFilterChain` can be unique and can be configured in isolation. In fact, a `SecurityFilterChain` might have zero
security `Filter` instances if the application wants Spring Security to ignore certain requests.

### Security Filters

The Security Filters are inserted into the `FilterChainProxy` with the `SecurityFilterChain` API. Those filters can be
used for a number of different purposes, like _exploit protection_, _authentication_, _authorization_ and more. The
filters are executed in a specific order to guarantee that they are invoked at the right time, for example, the `Filter`
that performs authentication should be invoked before the `Filter` that performs authorization. It is typically not
necessary to know the ordering of Spring Security’s `Filter`s. However, there are times that it is beneficial to know
the ordering, if you want to know them, you can check the `FilterOrderRegistration` code.

These security filters are most often declared using an `HttpSecurity` instance. To exemplify the above paragraph, let’s
consider the following security configuration:

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.csrf(Customizer.withDefaults())
            .httpBasic(Customizer.withDefaults())
            .formLogin(Customizer.withDefaults())
            .authorizeHttpRequests((authorize) -> authorize.anyRequest()
                                                           .authenticated());
        return http.build();
    }
}
```

The above configuration will result in the following `Filter` ordering:

| Filter                               | Added by                             |
|--------------------------------------|--------------------------------------|
| CsrfFilter                           | `HttpSecurity#csrf`                  |
| BasicAuthenticationFilter            | `HttpSecurity#httpBasic`             |
| UsernamePasswordAuthenticationFilter | `HttpSecurity#formLogin`             |
| AuthorizationFilter                  | `HttpSecurity#authorizeHttpRequests` |

1. The `CsrfFilter` is invoked to protect against CSRF attacks.
2. The authentication filters are invoked to authenticate the request.
3. The `AuthorizationFilter` is invoked to authorize the request.

> [!NOTE]
> There might be other `Filter` instances that are not listed above. If you want to see the list of filters invoked for
> a particular request, you can print them.

#### Printing the Security Filters

There are times it is useful to see the list of security `Filter`s that are invoked for a particular request. For
example, we want to make sure that the filter we have added is in the list of the security filters.

The list of filters is printed at `DEBUG` level on the application startup, so we can see something like the following
on the console output, for example:

```terminaloutput
0000-00-00T00:00:00.000-00:00  DEBUG 12345 --- [     main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with [ DisableEncodeUrlFilter, WebAsyncManagerIntegrationFilter, SecurityContextHolderFilter, HeaderWriterFilter, CsrfFilter, LogoutFilter, UsernamePasswordAuthenticationFilter, DefaultLoginPageGeneratingFilter, DefaultLogoutPageGeneratingFilter, BasicAuthenticationFilter, RequestCacheAwareFilter, SecurityContextHolderAwareRequestFilter, AnonymousAuthenticationFilter, ExceptionTranslationFilter, AuthorizationFilter]
```

And that will give a pretty good idea of the security filters that are configured for each filter chain.

#### Logging the Security Events

We can also configure our application to print the invocation of each filter for each request. That is helpful to see if
the filter we have added is invoked for a particular request or to check where an exception is coming from.

Spring Security provides comprehensive logging of all security-related events at the `DEBUG` and `TRACE` level. This can
be invaluable when debugging our application because, for security measures, Spring Security does not add any detail of
why a request has been rejected to the response body. If you come across a `401` or `403` error, it is very likely that
you will find a log message that will help you understand what is going on.

Let’s consider an example where a user tries to make a `POST` request to a resource that has _CSRF protection_ enabled
without the _CSRF_ token. With no logs, the user will see a `403` error with no explanation of why the request was
rejected. However, if we enable logging for Spring Security, we will see a log message like this:

```terminaloutput
0000-00-00T00:00:00.000-00:00 DEBUG 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Securing POST /hello
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking DisableEncodeUrlFilter (1/15)
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking WebAsyncManagerIntegrationFilter (2/15)
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking SecurityContextHolderFilter (3/15)
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking HeaderWriterFilter (4/15)
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.security.web.FilterChainProxy        : Invoking CsrfFilter (5/15)
0000-00-00T00:00:00.000-00:00 DEBUG 12345 --- [nio-8080-exec-1] o.s.security.web.csrf.CsrfFilter         : Invalid CSRF token found for http://localhost:8080/hello
0000-00-00T00:00:00.000-00:00 DEBUG 12345 --- [nio-8080-exec-1] o.s.s.w.access.AccessDeniedHandlerImpl   : Responding with 403 status code
0000-00-00T00:00:00.000-00:00 TRACE 12345 --- [nio-8080-exec-1] o.s.s.w.header.writers.HstsHeaderWriter  : Not injecting HSTS header since it did not match request to [Is Secure]
```

It becomes clear that the _CSRF_ token is missing and that is why the request is being denied.

To configure the application to log all the security events, add the following to the `application.properties` file:

`logging.level.org.springframework.security = TRACE`

or add the following to the `logback.xml` file:

```xml
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <!-- ... -->
    </appender>
    <!-- ... -->
    <logger name="org.springframework.security" level="trace" additivity="false">
        <appender-ref ref="Console"/>
    </logger>
</configuration>
```

**<mark>
Bottom line: Spring security is just a bunch of servlet Filters in a FilterChain that are added automatically when
Spring Security dependency is included. Those filters by default intercept all the requests and check if it needs
authentication, and if it does, then make sure it is authenticated to allow the request to proceed.
</mark>**

_But where are those `Filter`s and `FilterChain` configured?_ We will find out in the upcoming sections.

## Enabled TRACE Logging

As we have seen in the [Logging the Security Events](#logging-the-security-events) section, enabling `TRACE` logging
for `org.springframework.security` package will give us a lot of information about the security events.

We can see the below log message in the console output when the application starts:

```terminaloutput
0000-00-00T00:00:00.000+00:00 DEBUG 54321 --- [spring-boot-security-101] [           main] o.s.s.web.DefaultSecurityFilterChain     : Will secure any request with filters: DisableEncodeUrlFilter, WebAsyncManagerIntegrationFilter, SecurityContextHolderFilter, HeaderWriterFilter, CsrfFilter, LogoutFilter, UsernamePasswordAuthenticationFilter, DefaultResourcesFilter, DefaultLoginPageGeneratingFilter, DefaultLogoutPageGeneratingFilter, BasicAuthenticationFilter, RequestCacheAwareFilter, SecurityContextHolderAwareRequestFilter, AnonymousAuthenticationFilter, ExceptionTranslationFilter, AuthorizationFilter
```

Hitting the `http://localhost:8080/unsecured/home` endpoint using _curl_ with valid credentials will result in the below
log message in the console output:

```bash
curl -i -u "dev-user-1:dev-password-1" http://localhost:8080/unsecured/home
```

```terminaloutput
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Trying to match request against DefaultSecurityFilterChain defined as 'defaultSecurityFilterChain' in [class path resource [org/springframework/boot/security/autoconfigure/web/servlet/ServletWebSecurityAutoConfiguration$SecurityFilterChainConfiguration.class]] matching [any request] and having filters [DisableEncodeUrl, WebAsyncManagerIntegration, SecurityContextHolder, HeaderWriter, Csrf, Logout, UsernamePasswordAuthentication, DefaultResources, DefaultLoginPageGenerating, DefaultLogoutPageGenerating, BasicAuthentication, RequestCacheAware, SecurityContextHolderAwareRequest, AnonymousAuthentication, ExceptionTranslation, Authorization] (1/1)
0000-00-00T00:00:00.000+00:00 DEBUG 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Securing GET /unsecured/home
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking DisableEncodeUrlFilter (1/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking WebAsyncManagerIntegrationFilter (2/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking SecurityContextHolderFilter (3/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking HeaderWriterFilter (4/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking CsrfFilter (5/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] s.s.w.c.CsrfTokenRequestAttributeHandler : Wrote a CSRF token to the following request attributes: [_csrf, org.springframework.security.web.csrf.CsrfToken]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.csrf.CsrfFilter         : Did not protect against CSRF since request did not match IsNotHttpMethod [TRACE, HEAD, GET, OPTIONS]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking LogoutFilter (6/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.a.logout.LogoutFilter            : Did not match request to PathPattern [POST /logout]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking UsernamePasswordAuthenticationFilter (7/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] w.a.UsernamePasswordAuthenticationFilter : Did not match request to PathPattern [POST /login]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking DefaultResourcesFilter (8/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking DefaultLoginPageGeneratingFilter (9/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking DefaultLogoutPageGeneratingFilter (10/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] .w.a.u.DefaultLogoutPageGeneratingFilter : Did not render default logout page since request did not match [PathPattern [GET /logout]]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking BasicAuthenticationFilter (11/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.a.www.BasicAuthenticationFilter  : Found username 'dev-user-1' in Basic Authorization header
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] w.c.HttpSessionSecurityContextRepository : No HttpSession currently exists
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] .s.s.w.c.SupplierDeferredSecurityContext : Created SecurityContextImpl [Null authentication]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] .s.s.w.c.SupplierDeferredSecurityContext : Created SecurityContextImpl [Null authentication]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.authentication.ProviderManager     : Authenticating request with DaoAuthenticationProvider (1/1)
0000-00-00T00:00:00.000+00:00 DEBUG 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.a.dao.DaoAuthenticationProvider    : Authenticated user
0000-00-00T00:00:00.000+00:00 DEBUG 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.a.www.BasicAuthenticationFilter  : Set SecurityContextHolder to UsernamePasswordAuthenticationToken [Principal=org.springframework.security.core.userdetails.User [Username=dev-user-1, Password=[PROTECTED], Enabled=true, AccountNonExpired=true, CredentialsNonExpired=true, AccountNonLocked=true, Granted Authorities=[ROLE_USER]], Credentials=[PROTECTED], Authenticated=true, Details=WebAuthenticationDetails [RemoteIpAddress=0:0:0:0:0:0:0:1, SessionId=null], Granted Authorities=[ROLE_USER, FactorGrantedAuthority [authority=FACTOR_PASSWORD, issuedAt=2026-03-13T05:54:45.044790Z]]]
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking RequestCacheAwareFilter (12/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.s.HttpSessionRequestCache        : matchingRequestParameterName is required for getMatchingRequest to lookup a value, but not provided
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking SecurityContextHolderAwareRequestFilter (13/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking AnonymousAuthenticationFilter (14/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking ExceptionTranslationFilter (15/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Invoking AuthorizationFilter (16/16)
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] estMatcherDelegatingAuthorizationManager : Authorizing GET /unsecured/home
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] estMatcherDelegatingAuthorizationManager : Checking authorization on GET /unsecured/home using org.springframework.security.authorization.AuthenticatedAuthorizationManager@2925c6d2
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.a.AnonymousAuthenticationFilter  : Did not set SecurityContextHolder since already authenticated UsernamePasswordAuthenticationToken [Principal=org.springframework.security.core.userdetails.User [Username=dev-user-1, Password=[PROTECTED], Enabled=true, AccountNonExpired=true, CredentialsNonExpired=true, AccountNonLocked=true, Granted Authorities=[ROLE_USER]], Credentials=[PROTECTED], Authenticated=true, Details=WebAuthenticationDetails [RemoteIpAddress=0:0:0:0:0:0:0:1, SessionId=null], Granted Authorities=[ROLE_USER, FactorGrantedAuthority [authority=FACTOR_PASSWORD, issuedAt=2026-03-13T05:54:45.044790Z]]]
0000-00-00T00:00:00.000+00:00 DEBUG 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.security.web.FilterChainProxy        : Secured GET /unsecured/home
=> /UNSECURED/HOME
0000-00-00T00:00:00.000+00:00 TRACE 54321 --- [spring-boot-security-101] [nio-8080-exec-2] o.s.s.w.header.writers.HstsHeaderWriter  : Not injecting HSTS header since it did not match request to [Is Secure]
```

The above log message shows the entire security chain followed to get to the endpoint including the location, type
and name of the filter chain bean (`ServletWebSecurityAutoConfiguration$SecurityFilterChainConfiguration.class` =>
`DefaultSecurityFilterChain` => `defaultSecurityFilterChain`) and all the filters in the order they are applied.

> [!NOTE]
> Enabling the TRACE level logging is not recommended for production environments as it can generate a lot of noise but
> is useful in development environments for learning and debugging. We are going to keep it enabled throughout this
> tutorial.

> [!TIP]
> Try the curl command without credentials or with wrong credentials and see the log messages in the console output to
> understand what is different and what exceptions are thrown etc.

## Custom Security Configuration

With Spring Security, our application is secured. But it is kind of very strict that we cannot access any endpoints
without credentials. The built-in security filter chain is configured to secure every endpoint, and it will only allow
authenticated users to access any endpoints. What if we want to access a few endpoints without credentials? What if we
want to access some endpoints only with specific roles? This is where we start defining our own `SecurityFilterChain`
bean. It is like defining any other typical Spring bean. Since we will be defining multiple `SecurityFilterChain`s and
doing a lot of customizations, we will create a new class called `SecurityConfig` to hold the bean definition. Going
forward, any configuration related to Spring Security will be defined in this class.

Since we have no idea how a `SecurityFilterChain` bean will look like, we will use the
`SecurityFilterChainConfiguration` nested class from the `ServletWebSecurityAutoConfiguration` class as a starting
point. The `SecurityFilterChainConfiguration` class contains the default security filter chain bean configuration. We
will copy this bean definition and add it to our `SecurityConfig` class.

```java
@Bean
SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http) {
    // 1
    http.authorizeHttpRequests((requests) -> requests.anyRequest()
                                                     .authenticated());
    // 2
    http.formLogin(withDefaults());
    // 3
    http.httpBasic(withDefaults());
    // 4
    return http.build();
}
```

1. We are using the `authorizeHttpRequests` method to configure any request should be authenticated.
2. We are using the `formLogin` method with defaults to configure the built-in login page.
3. We are using the `httpBasic` method to with defaults to configure the HTTP Basic authentication.
4. Finally, we are returning the customized `HttpSecurity` object.

Once the `SecurityFilterChain` bean is created, we can see all the security filters in the chain in the below image.

<p align="center">
<img width="800px" alt="default-filters" src="https://github.com/user-attachments/assets/a07726d7-4c3e-4745-a247-fd3432dc4a7e">
</p>

> [!NOTE]
> The above approach will not make any difference in the behavior of the application as we are not modifying any of the
> configuration. Our production code works exactly the same way (endpoints are secured and cannot be accessed without
> authentication), and all our tests pass as earlier. We will override this configuration in the upcoming sections.

## Modifying The Custom Security Configuration

We have modified the security filter chain bean configuration by removing `authorizeHttpRequests`, `formLogin` and
`httpBasic` methods. The `authorizeHttpRequests` method was used to configure that any request should be authenticated,
the `formLogin` method was used to configure the built-in login page, and the `httpBasic` method was used to configure
the HTTP Basic authentication. Since we removed these methods, the security filter chain will not have these
configurations and probably few filters might have been removed. All our endpoints are accessible without
authentication. You will not see the login screen in the browser, and you don't have to pass any credentials in
_Postman_ or _curl_. Though we have Spring Security included in our application, none of the endpoints are secured,
and that's because of the way we have configured the security filter chain. This proves that anything and everything
related to Spring Security is configured via the `SecurityFilterChain` with respective Security Filters.

The below images shows that the `SecurityFilterChain` does not contain all the security filters existed earlier.

<p align="center">
<img width="800px" alt="few-filters-removed" src="https://github.com/user-attachments/assets/cbb76c62-968f-41b7-9f66-ca1172fe33b6">
</p>

> [!NOTE]
> We have removed the `formLogin` and `httpBasic` methods and that removed the `DefaultLoginPageGeneratingFilter` and
> `DefaultLogoutPageGeneratingFilter` filters. Since there is no form login and HTTP Basic authentication, there is no
> need for `UsernamePasswordAuthenticationFilter`, `BasicAuthenticationFilter`. Since there is no authentication, there
> is no authorization and no `AuthorizationFilter`. We do have `AnonymousAuthenticationFilter` to authenticate each
> request anonymously.

> [!WARNING]
> Two of our tests (`should_return_unauthorized_if_secured_endpoint_accessed_without_authorization` and
> `should_return_unauthorized_if_secured_endpoint_accessed_by_anonymous_user`) are failing now. These are the
> negative tests that are verifying that accessing our endpoints without authentication should return a
> `401 Unauthorized` and not `200 OK`. But now our endpoints are not secured and does not need authentication and always
> return `200 OK`. This makes the tests fail, and we will fix it in the upcoming sections.

> [!IMPORTANT]
> This is the beauty of having tests and especially negative tests. We can catch these kinds of bugs early and fix them
> before they become production issues. The more tests we have, the more confidence in changing our code and fewer
> production bugs we will have.

## Disabling Anonymous Authentication

We have disabled the Anonymous Authentication feature using the `anonymous()` method on the `http` object. This will
remove the `AnonymousAuthenticationFilter` security filter from the security filter chain which we can see in the below
image.

<p align="center">
<img width="800px" alt="anonymous-authentication-filter-removed" src="https://github.com/user-attachments/assets/e5406e5f-7e23-42a4-92fb-9250faaec17e">
</p>

If we compare this image with the previous image, we can see that the `AnonymousAuthenticationFilter` has been removed.

> [!NOTE]
> Disabling Anonymous Authentication does not change the behavior of the application. Our production code and our tests
> work exactly as before (with two failing tests). As we disscussed earlier, the only difference is that the
> `SecurityContextHolder` now contains null instead of an `Authentication` object. We will prove this in the upcoming
> sections.

> [!WARNING]
> Anonymous Authentication is a feature of Spring Security that is enabled by default. It is not recommended to
> disable it unless you have a valid reason to do so.
>
> Let's say you are accessing the `Authentication` object in any controller method, and you receive an unauthenticated
> request. If anonymous authentication is disabled, then chances are that the `Authentication` object will be null and
> you need to make sure that your code is handling this case appropriately.
>
> Another scenario is when you have tests that are not annotated with `@WithAnonymousUser` or `@WithMockUser`. If
> anonymous authentication is disabled, then you will end up with a null instead of an `Authentication` object.

## Improving The Controllers And Tests

Actually, we need to explore the `SecurityFilterChain` bean and different ways to customize it. But before we do
that, we are going to improve out controllers and tests. The reason we are doing this is to have a set of well-defined
endpoints and corresponding tests, so that we have something to verify when we start playing with the
`SecurityFilterChain` bean.

### Improving The Controllers

Our `SampleController` class is basic and uses very few features of Spring Security. To understand some of the
advanced features of Spring Security, we will improve the controllers to use them.

> [!NOTE]
> `Principal`, `Authentication`, `SecurityContext`, `SecurityContextHolder`, `GrantedAuthority` etc. are some of the
> important terms that we will be discussing in the upcoming sections. For now, know that they are there.

| Term                     | Descritpion                                                                                                                                            |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| `SecurityContextHolder`  | is where Spring Security stores the details of who is authenticated.                                                                                   |
| `SecurityContext`        | is obtained from the `SecurityContextHolder` and contains the `Authentication` of the currently authenticated user.                                    |
| `Authentication`         | is the input to `AuthenticationManager` to provide the credentials a user has provided to authenticate or the current user from the `SecurityContext`. |
| `GrantedAuthority`       | is granted to the principal on the `Authentication` (i.e. roles, scopes, etc.).                                                                        |
| `Principal`              | is the identity of the currently authenticated user.                                                                                                   |
| `AuthenticationManager`  | is the API that defines how Spring Security’s Filters perform authentication.                                                                          |
| `ProviderManager`        | is the most common implementation of `AuthenticationManager`.                                                                                          |
| `AuthenticationProvider` | is used by `ProviderManager` to perform a specific type of authentication.                                                                             |

We can inject the `Principal` object in the controller method and use it to get the current user accessing the endpoint.
We can achieve the same by injecting the `Authentication` object insead of the `Principal` object and then extract the
`Principal` object from the `Authentication` object using the `getPrincipal()` method. Even better, we can directly
inject the `SecurityContext` object and use the `getAuthentication()` method to get the `Authentication` object. Using
the `SecurityContext` is the recommended way because the `Authentication` may be null if anonymous authentication is
disabled.

> [!TIP]
> Injecting the `SecurityContext` object into the controller methods is a good practice because it makes the code
> more readable and maintainable, but it also makes the code depend on Spring and makes unit testing more difficult.
> You do not want to depend on Spring when doing a pure unit test. So instead of injecting the `SecurityContext` object
> into the controller methods we have used the `SecurityContextHolder`'s `getContext()` static method to get the
> `SecurityContext` object. This way we can construct the `SecurityContext` object with an `Authentication` object and
> place it in the `SecurityContextHolder` without having to depend on Spring.

### Improving The Tests

The tests we have now are a good start, but they are not enough. They were added without proper understanding of our
application or more importantly, the security configuration. In our [project structure](#project-structure), we have
clearly specified our requirement explaining how each endpoint should behave (secured, not secured, who can and cannot
access it etc.). We will be adding more tests that are aligned with our controller methods. We will also be adding a few
negative tests to verify that our endpoints are not accessible without proper authentication.

> [!WARNING]
> Many of our tests are failing now, and we will fix them in the upcoming sections. The reason for the failure is
> that we have removed the `@WithMockUser` and `@WithAnonymousUser` annotations and the basic auth from the tests and
> not yet customized the `SecurityFilterChain` bean properly. We have copied the default `SecurityFilterChain` bean from
> the `ServletWebSecurityAutoConfiguration` class and using it as is which will protect all the endpoints by default
> and does not reflect our custom security needs.

Bottom line: Our controllers, services, and tests are refactored and improved. We have added more tests to verify if our
endpoints are secured. We have the default security filter chain bean configured in the `SecurityConfig` class.

## Fixing SampleControllerUnitTest

If you run the tests from the `SampleControllerUnitTest` class, they all fail with the same reason. Check the below
attached error message.

```terminaloutput
Expected :[/UNSECURED/HOME] is accessed by [test-user] with role(s) [ROLE_USER]
Actual   :jakarta.servlet.ServletException: Request processing failed: java.lang.NullPointerException: Cannot invoke "org.springframework.security.core.Authentication.getPrincipal()" because "authentication" is null
```

It is a `NullPointerException` thrown by the `getPrincipal()` method of the `Authentication` object. It says that the
`Authentication` object is null. But why?

The `constructMessage(...)` method in our `SampleController` class is expecting an `Authentication` object from the
`SecurityContext`. The `SecurityContext` object is obtained from the `SecurityContextHolder`. If we have Spring Security
enabled in the project, by default every endpoint is secured and expects an authenticated user. That authenticated
user's details are available as an `Authentication` object and placed in the `SecurityContext`. The
`SecurityContextHolder` (as the name says) holds the `SecurityContext` object which can be retrieved from the
`SecurityContextHolder` using the `getContext()` static method. All these are done by the framework (Spring). Since ours
is a unit test, and we are not using any Spring support, we need to manually create the `Authentication` object and
place it in the `SecurityContext`. We are going to do this in the upcoming sections.

> [!NOTE]
> We could have added a null check in the `constructMessage(...)` method and returned a default message if the
> `Authentication` object is null like below:
>
> ```java
> private String constructMessage(final HttpServletRequest request) {
>   final SecurityContext securityContext = SecurityContextHolder.getContext();
>   final Authentication  authentication  = securityContext.getAuthentication();
>   if (authentication == null) {
>       return "No authentication found";
>   }
>   final String username = authentication.getPrincipal() instanceof UserDetails userDetails
>                           ? userDetails.getUsername()
>                           : authentication.getName();
>   ...
>   ...
> }
> ```
> We did not do that for the below two reasons:
>
> 1. To understand how to populate a dummy `Auhentication` object that can be used in tests.
> 2. To understand _Anonymous Authentication_. Because we will be facing the same issue when we disable _Anonymous
     Authentication_ later in this tutorial.

So How are we going to do it?

To solve the above problem, we need to understand how Spring's authentication model works.

## Authentication

The `Authentication` interface serves two main purposes within Spring Security:

1. An input to `AuthenticationManager` to provide the credentials a user has provided to authenticate or the current
   user from the `SecurityContext`. When used in this scenario, `isAuthenticated()` returns `false`.
2. Represent the currently authenticated user. We can get the current `Authentication` from the `SecurityContext`.

The `Authentication` contains:

* `principal`: Identifies the user. When authenticating with a username and password, this is often an instance of
  `UserDetails`.
* `credentials`: Often a password. In many cases, this is cleared after the user is authenticated to ensure that it is
  not leaked.
* `authorities`: The `GrantedAuthority` instances are high-level permissions the user is granted. Two examples are roles
  and scopes.

The `Authentication` interface have many implementations like `AnonymousAuthenticationToken`,
`UsernamePasswordAuthenticationToken`, `RememberMeAuthenticationToken`, `TestingAuthenticationToken`, etc. So wherever
`Authentication` is needed we can pass one of these implementations (_Polymorphism 101_). We can use any of these
implementations to create an `Authentication` object but `TestingAuthenticationToken` seems more appropriate as it is
specifically designed for unit tests.

## SecurityContext

The `SecurityContext` is obtained from the `SecurityContextHolder` and contains an `Authentication` object of the
currently authenticated user.

## SecurityContextHolder

`SecurityContextHolder` is the heart of Spring Security's authentication model. `SecurityContextHolder` contains the
`SecurityContext`. The `SecurityContextHolder` is where Spring Security stores the details of the currently
authenticated user. Spring Security does not care how the `SecurityContextHolder` is populated. If it contains a value,
it is used as the currently authenticated user. Check the below image for reference.

<p align="center">
<img width="800px" alt="security-context-holder" src="https://github.com/user-attachments/assets/0857fe8d-5ea6-4cd9-84bd-8b2de153a719">
</p>

The simplest way to indicate a user is authenticated is to set the `SecurityContextHolder` directly.

So the below is what we are going to do.

```java
// 1
final SecurityContext            securityContext = SecurityContextHolder.createEmptyContext();
// 2
final TestingAuthenticationToken authentication  = new TestingAuthenticationToken("test-user", "test-password", "ROLE_USER");
// 3
securityContext.setAuthentication(authentication);
// 4
SecurityContextHolder.setContext(securityContext);
```

1. We start by creating an empty `SecurityContext` object. We should create a new `SecurityContext` instance instead of
   using `SecurityContextHolder.getContext().setAuthentication(authentication)` to avoid race conditions across multiple
   threads.
2. Next, we create a new `Authentication` object. Spring Security does not care what type of `Authentication`
   implementation is set on the `SecurityContext`. Here, we use the `TestingAuthenticationToken`, with some dummy
   username, password, and role because it is appropriate. A more common production scenario is
   `UsernamePasswordAuthenticationToken(userDetails, password, authorities)`.
3. Next, we are setting the `Authentication` object on the `SecurityContext` object.
4. Finally, we are setting the `SecurityContext` object on the `SecurityContextHolder` object. Spring Security uses this
   information for authorization down the line.

> [!IMPORTANT]
> We are going to do the above in the `@BeforeEach` annotated method so that each test can have its own unique fresh
`Authentication` object.

> [!IMPORTANT]
> It is important to clear the `SecurityContext` from the `SecurityContextHolder` after every test, and we will do it in
> the `@AfterEach` annotated method.

To collect information about the authenticated user, access the `SecurityContextHolder`.

```java
SecurityContext context                            = SecurityContextHolder.getContext();
Authentication authentication                      = context.getAuthentication();
String username                                    = authentication.getName();
Object principal                                   = authentication.getPrincipal();
Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
```

> [!IMPORTANT]
> `SampleControllerUnitTest` is a pure unit test and does not depend on Spring. We are not testing anything related
> to security. The tests would have passed without doing anything about security. The security filters would be loaded
> only when Spring is in the picture. The only problem is that our `constructMessage(...)` method is desingned in a way
> that it expects an `Authentication` object from the `SecurityContext` and that's the only reason we are forced to
> create the `SecurityContext` and `Authentication` etc.
>
> Bottom line: It is a unit test, and it is not testing anything related to security, and it really doesn't care about
> security.

> [!TIP]
> In Spring MVC, we can resolve the current principal with `@AuthenticationPrincipal` and the full `SecurityContext`
> with `@CurrentSecurityContext`.

> [!NOTE]
> By default, `SecurityContextHolder` uses a `ThreadLocal` to store these details, which means that the
> `SecurityContext` is always available to methods in the same thread, even if the `SecurityContext` is not explicitly
> passed around as an argument to those methods. Using a `ThreadLocal` in this way is quite safe if we take care to
> clear the thread after the present principal’s request is processed. Spring Security’s `FilterChainProxy` ensures that
> the `SecurityContext` is always cleared.

## Customizing The SecurityFilterChain

As per our [requirements](#project-structure),

* anything under `/unsecured` is not secured and should be accessible to everyone without any authentication.
* anything under `/secured` is secured and should be accessible only to authenticated users with the appropriate roles.
* anything under `/api` is secured and should be accessible only to authenticated users with the appropriate roles.
* anything under `/secured-by-default` is secured and should be accessible only to authenticated users with or without
  any roles.
* anything under `/deny` should be denied to everyone irrespective of the authentication and roles.

So let's tweak the `SecurityFilterChain` bean to reflect this.

```java
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) {
        // 1
        http.csrf(csrf -> csrf.ignoringRequestMatchers("/unsecured/create", "/secured/admin/create",
                                                       "/api/admin/create"));
        // 2
        http.authorizeHttpRequests(requests -> requests.requestMatchers("/deny/**")
                                                       .denyAll()
                                                       // 3
                                                       .requestMatchers(HttpMethod.POST, "/unsecured/create")
                                                       .permitAll()
                                                       // 4
                                                       .requestMatchers(HttpMethod.GET, "/unsecured/**")
                                                       .permitAll()
                                                       // 5
                                                       .requestMatchers(HttpMethod.POST, "/secured/admin/create")
                                                       .hasRole("ADMIN")
                                                       // 6
                                                       .requestMatchers(HttpMethod.GET, "/secured/admin")
                                                       .hasRole("ADMIN")
                                                       // 7
                                                       .requestMatchers(HttpMethod.GET, "/secured/user")
                                                       .hasAnyRole("USER", "ADMIN")
                                                       // 8
                                                       .requestMatchers(HttpMethod.GET, "/secured/other")
                                                       .authenticated()
                                                       // 9
                                                       .requestMatchers("/secured/**")
                                                       .authenticated()
                                                       // 10
                                                       .requestMatchers(HttpMethod.POST, "/api/admin/create")
                                                       .hasRole("ADMIN")
                                                       // 11
                                                       .requestMatchers(HttpMethod.GET, "/api/admin")
                                                       .hasRole("ADMIN")
                                                       // 12
                                                       .requestMatchers(HttpMethod.GET, "/api/user")
                                                       .hasAnyRole("USER", "ADMIN")
                                                       // 13
                                                       .requestMatchers(HttpMethod.GET, "/api/other")
                                                       .authenticated()
                                                       // 14
                                                       .requestMatchers("/api/**")
                                                       .authenticated()
                                                       // 15
                                                       .requestMatchers(HttpMethod.GET, "/secured-by-default/**")
                                                       .authenticated()
                                                       // 16
                                                       .anyRequest()
                                                       .authenticated());
        // 17
        http.formLogin(Customizer.withDefaults());
        // 18
        http.httpBasic(Customizer.withDefaults());
        // 19
        return http.build();
    }
```

The above code is self-explanatory, but let's break it down.

1. Disabling `CSRF` protection for specific endpoints. Spring Security expects a `CSRF` token for all state changing
   requests (like `POST`, `PUT`, `PATCH`, `DELETE`) but we are not providing it in our tests. We will revisit this in
   the upcoming sections. Disabling `CSRF` protection completely is not a good practice.
2. Denying all the requests that matches `/deny/**` irrespective of the HTTP method.
3. Permitting any `POST` request that matches `/unsecured/create`.
4. Permitting any `GET` request that matches `/unsecured/**`.
5. Specifying that any `POST` request that matches `/secured/admin/create` requires an autheticated user with `ADMIN`
   role.
6. Specifying that any `GET` request that matches `/secured/admin` requires an autheticated user with `ADMIN` role.
7. Specifying that any `GET` request that matches `/secured/user` requires an autheticated user with `USER` or `ADMIN`
   role.
8. Specifying that any `GET` request that matches `/secured/other` requires an autheticated user with or without any
   roles.
9. Specifying that any requests that matches `/secured/**` requires an autheticated user. This is just a safety measure
   to make sure that we are not accidentally allowing access to secured endpoints.
10. Specifying that any `POST` request that matches `/api/admin/create` requires an autheticated user with `ADMIN` role.
11. Specifying that any `GET` request that matches `/api/admin` requires an autheticated user with `ADMIN` role.
12. Specifying that any `GET` request that matches `/api/user` requires an autheticated user with `USER` or `ADMIN`
    role.
13. Specifying that any `GET` request that matches `/api/other` requires an autheticated user with or without any roles.
14. Specifying that any requests that matches `/api/**` requires an autheticated user. This is just a safety measure to
    make sure that we are not accidentally allowing access to secured endpoints.
15. Specifying that any `GET` requests that matches `/secured-by-default/**` requires an autheticated user.
16. Specifying that any other request that does not match any of the above rules requires an authenticated user.
    This is just a safety measure to make sure that we are not accidentally allowing access to any secured endpoints.
17. Configuring `formLogin` to allow users to log in with their username and password if they are using a browser.
18. Configuring `httpBasic` to allow users to log in with their username and password if they are using any REST client.
19. Finally, we are building and returning the customized `SecurityFilterChain` object.

> [!NOTE]
> The reason for having the `/secured-by-default/1` and `/secured-by-default/2` endpoints are to prove that, any
> endpoint which is not excluded explicitly from security is secured by default. For now, we have included in the
> configuration, but we will remove them from the configuration and test that behavior in the upcoming sections.

> [!NOTE]
> We have refactored the name of the `SecurityFilterChain` bean from `defaultSecurityFilterChain` to
> `securityFilterChain` as it is not the default `SecurityFilterChain` bean provided by Spring Security but a custom
> bean that we have created to match our requirements.

## Fixing SampleControllerMockMvcUnitTest

We have our `SecurityFilterChain` bean configured exactly as per our requirements, let's fix the
`SampleControllerMockMvcUnitTest`.

There are totally 65 tests in the `SampleControllerMockMvcUnitTest` class and out of them 43 tests are failing
currently. They all fail for four different reasons.

All the below 24 tests are failing for the same reason. Check the attached error message below.

```terminaloutput
should return 200 ok when secured by default 1 is accessed by an authenticated user with admin role
should return 200 ok when secured other is accessed by an authenticated user with admin role
should return 200 ok when secured by default 2 is accessed by an authenticated user with admin role
should return 200 ok when secured by default 1 is accessed by an authenticated user with user role
should return 200 ok when secured other is accessed by an authenticated user with user role
should return 200 ok when api user is accessed by an authenticated user with user role
should return 200 ok when secured user is accessed by an authenticated user with admin role
should return 200 ok when secured admin is accessed by an authenticated user with admin role
should return 200 ok when api other is accessed by an authenticated user with user role
should return 200 ok when unsecured home is accessed by an unauthenticated user
should return 200 ok when api user is accessed by an authenticated user with admin role
should return 200 ok when unsecured about is accessed by an authenticated user
should return 200 ok when unsecured about is accessed by an unauthenticated user
should return 200 ok when secured by default 1 is accessed by an authenticated user without any role
should return 200 ok when secured user is accessed by an authenticated user with user role
should return 200 ok when unsecured home is accessed by an authenticated user
should return 200 ok when api other is accessed by an authenticated user with admin role
should return 200 ok when unsecured about is accessed by an anonymous user
should return 200 ok when secured by default 2 is accessed by an authenticated user without any role
should return 200 ok when secured by default 2 is accessed by an authenticated user with user role
should return 200 ok when api admin is accessed by an authenticated user with admin role
should return 200 ok when unsecured home is accessed by an anonymous user
should return 200 ok when secured other is accessed by an authenticated user without any role
should return 200 ok when api other is accessed by an authenticated user without any role
```

```terminaloutput
java.lang.AssertionError: Status expected:<200 OK> but was:<401 UNAUTHORIZED>
Expected :200 OK
Actual   :401 UNAUTHORIZED
```

All the below 5 tests are failing for the same reason. Check the attached error message below.

```terminaloutput
should return 201 created when unsecured create is accessed by an anonymous user
should return 201 created when unsecured create is accessed by an authenticated user
should return 201 created when unsecured create is accessed by an unauthenticated user
should return 201 ok when api admin create is accessed by an authenticated user with admin role
should return 201 created when secured admin create is accessed by an authenticated user with admin role
```

```terminaloutput
java.lang.AssertionError: Status expected:<201 CREATED> but was:<403 FORBIDDEN>
Expected :201 CREATED
Actual   :403 FORBIDDEN
```

All the below 10 tests are failing for the same reason. Check the attached error message below.

```terminaloutput
should return 403 forbidden when deny 1 is accessed by an authenticated user with user role
should return 403 forbidden when deny 1 is accessed by an authenticated user with admin role
should return 403 forbidden when api user is accessed by an authenticated user without admin or user role
should return 403 forbidden when deny 2 is accessed by an authenticated user with admin role
should return 403 forbidden when secured admin is accessed by an authenticated user without admin role
should return 403 forbidden when deny 2 is accessed by an authenticated user with user role
should return 403 forbidden when deny 1 is accessed by an authenticated user without any role
should return 403 forbidden when api admin is accessed by an authenticated user without admin role
should return 403 forbidden when deny 2 is accessed by an authenticated user without any role
should return 403 forbidden when secured user is accessed by an authenticated user without admin or user role
```

```terminaloutput
java.lang.AssertionError: Status expected:<403 FORBIDDEN> but was:<401 UNAUTHORIZED>
Expected :403 FORBIDDEN
Actual   :401 UNAUTHORIZED
```

All the below 4 tests are failing for the same reason. Check the attached error message below.

```terminaloutput
should return 401 unauthorized when secured admin create is accessed by an unauthenticated user
should return 401 unauthorized when api admin create is accessed by an unauthenticated user
should return 401 unauthorized when secured admin create is accessed by an anonymous user
should return 401 unauthorized when api admin create is accessed by an anonymous user
```

```terminaloutput
java.lang.AssertionError: Status expected:<401 UNAUTHORIZED> but was:<403 FORBIDDEN>
Expected :401 UNAUTHORIZED
Actual   :403 FORBIDDEN
```

Let's understand and fix them one by one. We will do this in two steps.

1. Our tests should know and use our custom `securityFilterChain` bean which we have created in the `SecurityConfig`
   class. Since `SampleControllerMockMvcUnitTest` is a slice test (annotated with @WebMvcTest), not all configurations
   are loaded automatically. So we need to explicitly import the `SecurityConfig` class in the test class so that our
   `securityFilterChain` bean is used by the test class.
2. Our tests should be modified so that they can run as unauthenticated/anonymous/authenticated users with appropriate
   roles to test the behavior of our `securityFilterChain`. We will do this using `@WithMockUser` and
   `@WithAnonymousUser` annotations.

* When no security-specific annotation is used on the test method, the test method is considered as unauthenticated but
  Spring Security will still create a user with name `anonymousUser` and role `ROLE_ANONYMOUS`.
* When `@WithAnonymousUser` annotation is used on the test method, the test method is considered as anonymously
  authenticated and Spring Security will create a user with name `anonymous` and role `ROLE_ANONYMOUS`.
* When `@WithMockUser` annotation is used on the test method, the test method is considered as authenticated and Spring
  Security will create a user with the name `user` and role `ROLE_USER`. We can customize the username and roles if
  needed.
    * Use `@WithMockUser` for those tests that are expected to run as an authenticated user with `USER` role since
      `ROLE_USER` is the default role for authenticated users. `@WithMockUser` is same as `@WithMockUser(roles = 
      "USER")`.
    * Use `@WithMockUser(roles = { "USER", "ADMIN" })` for those tests that are expected to run as an authenticated
      user with `ADMIN` role. Spring Security roles are flat by default and not hierarchical, meaning they do not
      automatically understand that a user with `ADMIN` role should have all the permissions of a user with `USER`
      role. So a user with `ADMIN` role should also be assigned `USER` role explicitly, otherwise `USER` role
      related permissions will not be available to the user with just `ADMIN` role.
    * Use `@WithMockUser(roles = "")` for those tests that are expected to run as an authenticated user without any
      roles.

> [!NOTE]
> The difference between unauthenticated test and anonymously authenticated test is very subtle and no visible
> difference in the test output. But we will explore the difference later when we disable anonymous authentication in
> the `SecurityConfig` class.

## Fixing SampleControllerMockIntegrationTest

The `SampleControllerMockIntegrationTest` is similar to `SampleControllerMockMvcUnitTest` except that it is annotated
with `@SpringBootTest` instead of `@WebMvcTest`. Because it is a `@SpringBootTest`, we do not need to explicitly import
the `SecurityConfig` class in the test instead all the configurations are loaded automatically. We still need to use
`@WithMockUser` and `@WithAnonymousUser` annotations on the tests because this is an integration test that runs on a
mock server and not on the real server.

## Fixing SampleControllerServerIntegrationTest

The `SampleControllerServerIntegrationTest` is annotated with `@SpringBootTest` and it is a complete integration test
that runs on a real server. We do not need to import the `SecurityConfig` class in the test because all the
configurations are loaded automatically. We cannot use `@WithMockUser`, `@WithAnonymousUser` anotations or mock
credentials anymore for authentication. We need real users, and we are going to use the one that we have created earlier
using the `@TestPropertySource` annotation. But we still have a problem. We need users with different roles for
different tests. For example,

* We need a user with `ADMIN` role (it includes `USER` and `ADMIN` role implicitly).
* We need a user with only `USER` role.
* We need a user without any role.

But we cannot create multiple users in the `@TestPropertySource` annotation. For now, we do not have a solution for this
problem. So some of our tests will fail. We will fix them once we start creating users programmatically in our
`SecurityConfig` class.

## Authentication Flow

We have talked about authentication in the previous sections, and we are going to extend that knowledge further.

### GrantedAuthority

`GrantedAuthority` instances are high-level permissions that the user is granted. Two examples are _roles_ and _scopes_.

You can get `GrantedAuthority` instances from the `Authentication.getAuthorities()` method. This method provides a
`Collection` of `GrantedAuthority` objects. A `GrantedAuthority` is, not surprisingly, an authority granted to the
principal. Such authorities are usually "roles" such as `ROLE_ADMINISTRATOR` or `ROLE_HR_SUPERVISOR`. These roles are
later configured for web authorization, method authorization, and domain object authorization. Other parts of Spring
Security interpret these authorities and expect them to be present. When using username/password based authentication
`GrantedAuthority` instances are usually loaded by the `UserDetailsService`. Usually, the `GrantedAuthority` objects are
application-wide permissions.

### AuthenticationManager

`AuthenticationManager` is the API that defines how Spring Security’s Filters perform authentication. The
`Authentication` that is returned is then set on the `SecurityContextHolder` by the controller (that is, by Spring
Security’s Filters instances) that invoked the `AuthenticationManager`. If you are not integrating with Spring
Security’s Filters instances, you can set the `SecurityContextHolder` directly and are not required to use an
`AuthenticationManager`.

While the implementation of `AuthenticationManager` could be anything, the most common implementation is
`ProviderManager`.

### ProviderManager

`ProviderManager` is the most commonly used implementation of `AuthenticationManager`. `ProviderManager` delegates to a
List of `AuthenticationProvider` instances. Each `AuthenticationProvider` has an opportunity to indicate that
authentication should be successful, fail, or indicate it cannot make a decision and allow a downstream
`AuthenticationProvider` to decide. If none of the configured `AuthenticationProvider` instances can authenticate,
authentication fails with a `ProviderNotFoundException`, which is a special `AuthenticationException` that indicates
that the `ProviderManager` was not configured to support the type of `Authentication` that was passed into it.

<p align="center">
<img width="900px" alt="provider-manager" src="https://github.com/user-attachments/assets/8cf12c94-c658-4bdc-9087-6fa95aae883e">
</p>

In practice each `AuthenticationProvider` knows how to perform a specific type of authentication. For example, one
`AuthenticationProvider` might be able to validate a username/password, while another might be able to authenticate a
`SAML` assertion. This lets each `AuthenticationProvider` do a very specific type of authentication while supporting
multiple types of authentication and expose only a single `AuthenticationManager` bean.

`ProviderManager` also allows configuring an optional parent `AuthenticationManager`, which is consulted in the event
that no `AuthenticationProvider` can perform authentication. The parent can be any type of `AuthenticationManager`, but
it is often an instance of `ProviderManager`.

<p align="center">
<img width="400px" alt="authentication-manager" src="https://github.com/user-attachments/assets/02073131-f27c-419e-b629-cde49fa6340c">
</p>

In fact, multiple `ProviderManager` instances might share the same parent `AuthenticationManager`. This is somewhat
common in scenarios where there are multiple `SecurityFilterChain` instances that have some authentication in common
(the shared parent `AuthenticationManager`), but also different authentication mechanisms (the different
`ProviderManager` instances).

<p align="center">
<img width="800px" alt="multiple-provider-manager" src="https://github.com/user-attachments/assets/ce630cda-8093-430b-8504-a730302b45dc">
</p>

By default, `ProviderManager` tries to clear any sensitive credentials information from the `Authentication` object that
is returned by a successful authentication request. This prevents information, such as passwords, being retained longer
than necessary in the HttpSession.

> [!NOTE]
> The CredentialsContainer interface plays a critical role in the authentication process. It allows for the erasure of
> credential information once it is no longer necessary, thereby enhancing security by ensuring sensitive data is not
> retained longer than necessary.

This may cause issues when you use a cache of user objects, for example, to improve performance in a stateless
application. If the `Authentication` contains a reference to an object in the cache (such as a `UserDetails` instance)
and this has its credentials removed, it is no longer possible to authenticate against the cached value. You need to
take this into account if you use a cache. An obvious solution is to first make a copy of the object, either in the
cache implementation or in the `AuthenticationProvider` that creates the returned `Authentication` object.
Alternatively, you can disable the `eraseCredentialsAfterAuthentication` property on `ProviderManager`.

### AuthenticationProvider

You can inject multiple `AuthenticationProvider` instances into `ProviderManager`. Each `AuthenticationProvider`
performs a specific type of authentication. For example, `DaoAuthenticationProvider` supports username/password-based
authentication, while `JwtAuthenticationProvider` supports authenticating a JWT token.

### Request Credentials with AuthenticationEntryPoint

`AuthenticationEntryPoint` is used to send an `HTTP` response that requests credentials from a client.

When a client makes an unauthenticated request to a protected resource, an implementation of `AuthenticationEntryPoint`
is used to request credentials from the client. The `AuthenticationEntryPoint` implementation might perform a redirect
to a log-in page, respond with an `WWW-Authenticate` header, or take other action.

Sometimes, a client proactively includes credentials (such as a username and password) to request a resource. In these
cases, Spring Security does not need to provide an HTTP response that requests credentials from the client, since they
are already included.

### AbstractAuthenticationProcessingFilter

`AbstractAuthenticationProcessingFilter` is used as a base Filter for authenticating a user’s credentials. Before the
credentials can be authenticated, Spring Security typically requests the credentials using `AuthenticationEntryPoint`.

Next, the `AbstractAuthenticationProcessingFilter` can authenticate any authentication requests that are submitted to
it.

<p align="center">
<img width="1200px" src="https://github.com/user-attachments/assets/063617b0-36d8-440a-84e9-be21d05b412e">
</p>

1. When the user submits their credentials, the AbstractAuthenticationProcessingFilter creates an Authentication from
   the HttpServletRequest to be authenticated. The type of Authentication created depends on the subclass of
   AbstractAuthenticationProcessingFilter. For example, UsernamePasswordAuthenticationFilter creates a
   UsernamePasswordAuthenticationToken from a username and password that are submitted in the HttpServletRequest.
2. Next, the `Authentication` is passed into the `AuthenticationManager` to be authenticated.
3. If authentication fails, then _Failure_.
    * The `SecurityContextHolder` is cleared out.
    * `RememberMeServices.loginFail` is invoked. If remember me is not configured, this is a no-op.
    * `AuthenticationFailureHandler` is invoked.
4. If authentication is successful, then _Success_.
    * `SessionAuthenticationStrategy` is notified of a new login.
    * Any already-authenticated `Authentication` in the `SecurityContextHolder` is loaded and its authorities are added
      to the returned `Authentication`.
    * The `Authentication` is set on the `SecurityContextHolder`. Later, if you need to save the `SecurityContext` so
      that it can be automatically set on future requests, `SecurityContextRepository#saveContext` must be explicitly
      invoked.
    * `RememberMeServices.loginSuccess` is invoked. If remember me is not configured, this is a no-op.
    * `ApplicationEventPublisher` publishes an `InteractiveAuthenticationSuccessEvent`.
    * `AuthenticationSuccessHandler` is invoked.
