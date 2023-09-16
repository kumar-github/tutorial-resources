# Spring Boot Actuator Demo
> **Every day is a learning day.**

This project will walk you through a simple demo of Spring Boot Actuator. It will help you in understanding **Spring Boot Actuator** module step by step. Each concept is covered in-depth and organized as individual git commits. The commits are numbered sequentially starting from `00`, `01`, `02` and so on. As you go through it, take the time to understand the changes each step makes to the code. You can clone the entire project to your local machine and then start applying the commits one by one starting from `00`. This `README.md` file will be updated in every commit and will tell you what has been covered in the specific commit.

<br/>

---

---

---

<br/>

TOC
===

* [Spring Boot Actuator Demo](#spring-boot-actuator-demo)
    * [Few things about Spring Boot Actuator](#few-things-about-spring-boot-actuator)
    * [Complete Reference](#complete-reference)
    * [Requirements](#requirements)
    * [Run Locally](#run-locally)
    * [Commit-00 :sparkles:](#commit-00-sparkles)
        * [Project Creation](#project-creation)
        * [Start the Project and Observe the Console Log](#start-the-project-and-observe-the-console-log)
        * [Access Various Endpoints](#access-various-endpoints)
     
<br/>

---

---

---

<br/>
     

## Few things about Spring Boot Actuator

*Spring Boot Actuator* module helps you monitor and manage and interact with your application. You can choose to manage and monitor your application using **HTTP** endpoints or with **JMX**. Spring Boot includes a number of built-in endpoints and lets you add your own.

You can *enable* or *disable* each individual endpoint and *expose* them (make them remotely accessible) over **HTTP** or **JMX**. An endpoint is considered to be *available* when it is both *enabled* and *exposed*. The built-in endpoints are auto configured only when they are available. Most applications choose exposure over HTTP, where the `ID` of the endpoint and a prefix of `/actuator` is mapped to a URL.
For example, by default, the health endpoint is mapped to `/actuator/health`.

<br/>

---

---

---

<br/>

## Complete Reference

[Spring Boot Actuator Reference](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)

<br/>

---

---

---

<br/>

## Requirements

* OpenJDK 17+
* Apache Maven 3.9.4+


<br/>

---

---

---

<br/>

## Run Locally

Clone the project

```bash
git clone https://github.com/kumar-github/spring-boot-actuator-demo
```

Go to the project root directory

```bash
cd spring-boot-actuator-demo
```

Start the service

```bash
./mvnw spring-boot:run
```

<br/>

---

---

---

<br/>

## Commit-00 :sparkles:

*This is the Initial Commit.*

| **Agenda for this commit**                                    |      Covered?      |
|---------------------------------------------------------------|:------------------:|
| 1. Project creation with needed dependencies.                 | :white_check_mark: |
| 2. Observe actuator related console log.                      | :white_check_mark: |
| 3. Hit the actuator endpoints over **HTTP**.                  | :white_check_mark: |
| 4. Talk about `Health` endpoint.                              | :white_check_mark: |
| 5. Access the actuator endpoints over **JMX** using jconsole. | :white_check_mark: |
| 6. Talk briefly about `Info` endpoint.                        | :white_check_mark: |

### Project Creation

We have created a spring boot project via [Spring Initializr](https://start.spring.io/) with below dependencies.

1. `spring-boot-starter-web`
2. `spring-boot-starter-actuator`
3. `spring-boot-devtools`

*Note: `spring-boot-devtools` is not mandatory but i am using here because of the live reload feature which will save some development time.*

### Start the Project and Observe the Console Log

At this point, the application can be started and accessed on `http://localhost:9090`. Though accessing `http://localhost:9090` will give you a `Whitelabel Error Page` but that is understandable since we did not map any controller to handle the request.

But we can find the below line logged in the console which is the proof that actuator is enabled.

```console
Exposing 1 endpoint(s) beneath base path '/actuator'
```

### Access Various Endpoints

Once the service is started, open your browser and hit `http://localhost:9090/actuator`. You will be getting a response that is similar to this

```json
{
  "_links": {
    "self": {
      "href": "http://localhost:9090/actuator",
      "templated": false
    },
    "health": {
      "href": "http://localhost:9090/actuator/health",
      "templated": false
    },
    "health-path": {
      "href": "http://localhost:9090/actuator/health/{*path}",
      "templated": true
    }
  }
}
```

The `health` endpoint is the only one that is exposed by default over **HTTP**. Though the other endpoints are enabled, they are **not exposed** over ***HTTP*** (but ***exposed*** over **JMX**) because of security reasons. You can access the `health` endpoint by clicking the link or hitting `http://localhost:9090/actuator/health` which will give you the below response.

```json
{
  "status": "UP"
}
```

*Note: Though the `health` endpoint is exposed, it is not giving all the details except the status of the service. We will tweak this in a moment to provide more details.*

Besides, you can access all the endpoints over **JMX** using `jconsole` from your `Terminal`.

*Note: Compared to **HTTP**, **JMX** is considered to be secure and all the endpoints are exposed by default. Below is the sample screenshot of the endpoints exposed via **JMX**.*

![Endpoints Over JMX](https://github.com/kumar-github/tutorial-resources/assets/2657313/f192df0d-59cb-4a32-8f55-e7c031ee6e59)

You can access the individual endpoints like `beans`, `health`, `info` etc here.

*Note: Spend some time exploring few endpoints like `beans`, `health`, `info` etc.*

*Note: The `info` endpoint does not return any information at the moment but we are going to fix it soon.*

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-01 :sparkles:

| **Agenda for this commit**                  |      Covered?      |
|---------------------------------------------|:------------------:|
| 1. Customizing JMX Domain.                  | :white_check_mark: |
| 2. Customizing Management Server Base Path. | :white_check_mark: |
| 3. Customizing the Management Server Port.  | :white_check_mark: |
| 4. Customizing the Web Endpoints Base Path. | :white_check_mark: |

### Customizing JMX Domain

By default, Spring Boot exposes all management endpoints as **JMX** MBeans under the `org.springframework.boot` (except `shutdown`) domain. You can customize the **JMX** domain under which endpoints are exposed using the `management.endpoints.jmx.domain` property as below.

```properties
management.endpoints.jmx.domain=tech.badprogrammer.app
```

*Below is the sample screenshot after changing the **JMX** domain.*

![JMX Custom Domain](https://github.com/kumar-github/tutorial-resources/assets/2657313/6d371fad-e2eb-4b31-8338-d7202c02967a)

### Customizing the Management Server Port

By default management endpoints are exposed on the same HTTP port in which the service is running. But it is possible to expose them on a different HTTP port, using the `management.server.port` property as below.

```properties
management.server.port=9123
```

After changing the management server port like above, the way to access the endpoints over **HTTP** is as below

```http
http://localhost:9123/actuator
```

### Customizing the Management Server Base Path

By default the *management server's base path* is configured to `/` and the *web endpoints base path* is configured to `actuator`. So the default way to access any endpoint over **HTTP** is the *management server base path* followed by the *web endpoints base path* which is `/actuator`

If needed, the management server's base path can be configured using the `management.server.base-path` property as below

```properties
management.server.base-path=/management
```

After changing the management server base path like above, the way to access the endpoints over **HTTP** is as below

```http
http://localhost:9123/management/actuator
```

*Note: The management sever base path change will work only if a custom management server port is configured.*

### Customizing the Web Endpoints Base Path

By default all the web endpoints are grouped under `/actuator` endpoint. But sometimes, it is useful to customize the prefix of the endpoints. For example, our application might already use `/actuator` for another purpose. We can use the `management.endpoints.web.base-path` property to change the prefix for the web endpoint as  below.

```properties
management.endpoints.web.base-path=/admin
```

*Note: The above path is relative to the servlet context path when the management server is sharing the main server port and relative to the management server base path when a separate management server port is configured.*

After configuring the web endpoints base path like above, the endpoints can be accessed by either

```http
http://localhost:9090/admin
```

or

```http
http://localhost:9123/management/admin
```

depends on the `management.server.port` property configuration.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-02:sparkles:

| **Agenda for this commit**                            |      Covered?      |
|-------------------------------------------------------|:------------------:|
| 1. Enabling All Endpoints.                            | :white_check_mark: |
| 2. Enabling Individual Endpoints (`shutdown`).        | :white_check_mark: |
| 3. Talk briefly about CONDITION EVALUATION DELTA.     | :white_check_mark: |
| 4. Disabling All Endpoints.                           | :white_check_mark: |
| 5. Disabling Individual Endpoints (`health`, `info`). | :white_check_mark: |

### Enabling All Endpoints

By default all management endpoints are **enabled** (except `shutdown`). You don't have to do anything extra to enable them.

### Enabling Individual Endpoints

If you want to enable individual endpoints, you can use the respective `management.endpoint.<ENDPOINT_ID>.enabled` property. To enable the `shutdown` endpoint, use the `management.endpoint.shutdown.enabled` property as below.

~~~properties
management.endpoint.shutdown.enabled=true
~~~

Since the `shutdown` endpoint is **enabled** now, it is automatically **exposed** over **JMX**. *Check the below screenshot.*

![Shutdown Endpoint Enabled](https://github.com/kumar-github/tutorial-resources/assets/2657313/3fa9152a-ea61-4799-a0fc-b4f65191ccce)

### Quick note about Spring's Auto Configuration.

If `spring-boot-dev-tools` dependency is available in the classpath, then you can observe the below CONDITION EVALUATION DELTA in the console log after enabling the `shutdown` endpoint.

```console
0000-00-00T00:00:00.000+00:00  INFO 33229 --- [  restartedMain] .ConditionEvaluationDeltaLoggingListener : Condition evaluation delta:

==========================
CONDITION EVALUATION DELTA
==========================

Positive matches:
-----------------

   ShutdownEndpointAutoConfiguration matched:
      - @ConditionalOnAvailableEndpoint marked as exposed by a 'management.endpoints.jmx.exposure' property (OnAvailableEndpointCondition)

   ShutdownEndpointAutoConfiguration#shutdownEndpoint matched:
      - @ConditionalOnMissingBean (types: org.springframework.boot.actuate.context.ShutdownEndpoint; SearchStrategy: all) did not find any beans (OnBeanCondition)

Negative matches:
-----------------

    None

Exclusions:
-----------

    None

Unconditional classes:
----------------------

    None
```

Since we enabled the `shutdown` endpoint in the `application.properties`, that triggered the `ShutdownEndpointAutoConfiguration` which then created the `ShutdownEndpoint` bean automatically.

### Disabling All Endpoints

If you want to disable all the endpoints, you can use the `management.endpoints.enabled-by-default` property as below.

~~~properties
management.endpoints.enabled-by-default=false
~~~

After setting the `management.endpoints.enabled-by-default` property to `false`, all the ebdpoints are **disabled** and it is **exposed**. *Check the below screenshot.*

![All Endpoints Disabled](https://github.com/kumar-github/tutorial-resources/assets/2657313/a7be2812-7f5f-4070-b5fa-05347ecea94a)

*Note: A **disabled** endpoint will not be exposed neither over **JMX** nor over **HTTP**.*

### Disabling Individual Endpoints

If you want to disable an individual endpoint, you can use the respective `management.endpoint.<ENDPOINT_ID>.enabled` property as below.

~~~properties
management.endpoint.health.enabled=false
management.endpoint.info.enabled=false
management.endpoint.beans.enabled=false
~~~

After disabling `health`, `info`, `beans` endpoints, they are not exposed. *Check the below screenshot.*

![Health-Info-Beans Endpoints Disabled](https://github.com/kumar-github/tutorial-resources/assets/2657313/371bc9ce-8a5c-4747-91fd-f83f2bbb4104)

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
