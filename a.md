# Spring Boot Security 101

Spring Security is a powerful and highly customizable authentication and authorization (access-control) framework. It
also provides protection against common attacks like CSRF etc. It is the de-facto standard for securing Spring-based
applications.
We will explore the fundamentals of Spring Security in a more detailed and practical manner.

## Project Structure

We have a simple rest controller which talks to a service and returns the response as a string. The controller contains
a bunch of endpoints that are not secured initially, but we will secure them using Spring Security. There is a reason
for having these many endpoints (methods). We will apply different security configurations to each set of endpoints and
see how they behave. The idea is as follows:

| S.No. | Endpoint                | Description                                                                        |
|:-----:|:------------------------|:-----------------------------------------------------------------------------------|
|   1   | `/unsecured/home`       | **NOT SECURED**. Accessible to everyone irrespective of the authentication status. |
|   2   | `/unsecured/about`      | **NOT SECURED**. Accessible to everyone irrespective of the authentication status. |
|       |                         |                                                                                    |
|   3   | `/secured/admin`        | **SECURED**. Accessible only to authenticated users with the role `ADMIN`.         |
|   4   | `/secured/user`         | **SECURED**. Accessible only to authenticated users with role `ADMIN` or `USER`.   |
|   5   | `/secured/other`        | **SECURED**. Accessible only to authenticated users with any roles.                |
|       |                         |                                                                                    |
|   6   | `/api/admin`            | **SECURED**. Accessible only to authenticated users with the role `ADMIN`.         |
|   7   | `/api/user`             | **SECURED**. Accessible only to authenticated users with role `ADMIN` or `USER`.   |
|   8   | `/api/other`            | **SECURED**. Accessible only to authenticated users with any roles.                |
|       |                         |                                                                                    |
|   9   | `/secured-by-default/1` | **SECURED BY DEFAULT**. Accessible only to authenticated users.                    |
|  10   | `/secured-by-default/2` | **SECURED BY DEFAULT**. Accessible only to authenticated users.                    |
|       |                         |                                                                                    |
|  11   | `/deny/1`               | **SECURED**. Access **DENIED** to all users.                                       |
|  12   | `/deny/2`               | **SECURED**. Access **DENIED** to all users.                                       |

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
> Both `SampleControllerUnitTest` and `SampleControllerMockMvcUnitTest` are unit tests but with different approaches.
> They behave differently when we introduce Spring Security into our project. We will see how to update them to make
> it work with Spring Security.

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
> focuses solely on the controller's logic in isolation. So it does not test any security-specific behavior.

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

To set up a `MockMvc` in a `@SpringBootTest` is like below:

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
