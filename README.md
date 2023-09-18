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
    * [Talk About Accessing Endpoints Over HTTP and JMX](#talk-about-accessing-endpoints-over-http-and-jmx)
  * [Commit-01 :sparkles:](#commit-01-sparkles)
    * [Customizing JMX Domain](#customizing-jmx-domain)
    * [Customizing the Management Server Port](#customizing-the-management-server-port)
    * [Customizing the Management Server Base Path](#customizing-the-management-server-base-path)
    * [Customizing the Web Endpoints Base Path](#customizing-the-web-endpoints-base-path)
  * [Commit-02 :sparkles:](#commit-02-sparkles)
    * [Enabling All Endpoints](#enabling-all-endpoints)
    * [Enabling Individual Endpoints](#enabling-individual-endpoints)
    * [Quick note about Spring's Auto Configuration.](#quick-note-about-springs-auto-configuration)
    * [Disabling All Endpoints](#disabling-all-endpoints)
    * [Disabling Individual Endpoints](#disabling-individual-endpoints)
  * [Commit-03 :sparkles:](#commit-03-sparkles)
    * [Removed All the Previous Configs](#removed-all-the-previous-configs)
  * [Commit-04 :sparkles:](#commit-04-sparkles)
    * [Exposing JMX Endpoints](#exposing-jmx-endpoints)
    * [Hiding All JMX Endpoints](#hiding-all-jmx-endpoints)
    * [Hiding Individual JMX Endpoints](#hiding-individual-jmx-endpoints)
    * [Exposing HTTP Endpoints](#exposing-http-endpoints)
    * [Exposing All HTTP Endpoints](#exposing-all-http-endpoints)
    * [Exposing Individual HTTP Endpoints](#exposing-individual-http-endpoints)
    * [Hiding All HTTP Endpoints](#hiding-all-http-endpoints)
    * [Hiding Individual HTTP Endpoints](#hiding-individual-http-endpoints)
  
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
| 1. Project creation with required dependencies.               | :white_check_mark: |
| 2. Observe actuator related console log.                      | :white_check_mark: |
| 3. Access the actuator endpoints over **HTTP**.               | :white_check_mark: |
| 4. Talk about `health` endpoint.                              | :white_check_mark: |
| 5. Access the actuator endpoints over **JMX** using jconsole. | :white_check_mark: |
| 6. Talk briefly about `info` endpoint.                        | :white_check_mark: |

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

### Talk About Accessing Endpoints Over HTTP and JMX

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

The `health` endpoint is the only one that is exposed over **HTTP** by default. Though other endpoints are enabled, they are **not exposed** over ***HTTP*** (but ***exposed*** over **JMX**) because of security reasons. You can access the `health` endpoint through `http://localhost:9090/actuator/health` which will give you the below response.

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

![JMX Custom Domain](https://github.com/kumar-github/tutorial-resources/assets/2657313/3c756d7d-ebb6-4c83-a7f1-a0f45b123d65)


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

## Commit-02 :sparkles:

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

To enable individual endpoints, you can use the respective `management.endpoint.<ENDPOINT_ID>.enabled` property. To enable the `shutdown` endpoint, use the `management.endpoint.shutdown.enabled` property as below.

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

To disable all the endpoints, you can use the `management.endpoints.enabled-by-default` property as below.

~~~properties
management.endpoints.enabled-by-default=false
~~~

After setting the `management.endpoints.enabled-by-default` property to `false`, all the endpoints are **disabled** and hence **not exposed**. *Check the below screenshot.*

![All Endpoints Disabled](https://github.com/kumar-github/tutorial-resources/assets/2657313/a7be2812-7f5f-4070-b5fa-05347ecea94a)

*Note: A **disabled** endpoint will not be exposed neither over **JMX** nor over **HTTP**.*

*Note: The `shutdown` endpoint is visible because we **enabled** it individually.*

### Disabling Individual Endpoints

To disable an individual endpoint, you can use the respective `management.endpoint.<ENDPOINT_ID>.enabled` property as below.

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

## Commit-03 :sparkles:

| **Agenda for this commit**            |      Covered?      |
|---------------------------------------|:------------------:|
| 1. Removing all the previous configs. | :white_check_mark: |

### Removed All the Previous Configs
Removed all the previously made config changes so that we can start fresh.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-04 :sparkles:

| **Agenda for this commit**             |      Covered?      |
|----------------------------------------|:------------------:|
| 1. Exposing JMX Endpoints.             | :white_check_mark: |
| 2. Hiding All JMX Endpoints.           | :white_check_mark: |
| 3. Hiding Individual JMX Endpoints.    | :white_check_mark: |
| 4. Exposing HTTP Endpoints.            | :white_check_mark: |
| 5. Exposing All HTTP Endpoints.        | :white_check_mark: |
| 6. Exposing Individual HTTP Endpoints. | :white_check_mark: |
| 7. Hiding All HTTP Endpoints.          | :white_check_mark: |
| 8. Hiding Individual HTTP Endpoints.   | :white_check_mark: |

### Exposing JMX Endpoints

All **enabled** endpoints are by default **exposed** over **JMX**. You don't have to do anything extra.

### Hiding All JMX Endpoints

To **hide** (not expose) all the endpoints that are **exposed** over **JMX**, use the `management.endpoints.jmx.exposure.exclude` property as below.

~~~properties
management.endpoints.jmx.exposure.exclude=*
~~~

![All JMX Endpoints Excluded](https://github.com/kumar-github/tutorial-resources/assets/2657313/98d960ec-51ea-4da5-a766-4a1e42bab72a)

### Hiding Individual JMX Endpoints

To **hide** (not expose) individual endpoints that are **exposed** over **JMX**, use the `management.endpoints.jmx.exposure.exclude` property as below.

~~~properties
management.endpoints.jmx.exposure.exclude=beans,health,info
~~~

![JMX Endpoints beans-health-info Excluded](https://github.com/kumar-github/tutorial-resources/assets/2657313/3ad8cf00-7e3c-4b18-9fb6-7adae06462c6)

### Exposing HTTP Endpoints

Although the endpoints are **enabled** by default, most of them are not exposed (except `http` endpoint) over **HTTP** like **JMX**.

### Exposing All HTTP Endpoints

To expose all the (*enabled*) endpoints over **HTTP**, use the `management.endpoints.web.exposure.include` property as below.

~~~properties
management.endpoints.web.exposure.include=*
~~~

![All HTTP Endpoints Exposed](https://github.com/kumar-github/tutorial-resources/assets/2657313/037b4cc9-5037-4da4-88c4-e937a0760e28)

### Exposing Individual HTTP Endpoints

Any (*enabled*) endpoint can be exposed over **HTTP** by using the `management.endpoints.web.exposure.include` property as below.

~~~properties
management.endpoints.web.exposure.include=beans,health,info
~~~

![HTTP Endpoints beans-health-info Exposed](https://github.com/kumar-github/tutorial-resources/assets/2657313/96629de9-993f-4dfb-8380-2c8eacb0d85a)

### Hiding All HTTP Endpoints

To hide (not expose) all endpoints over **HTTP**, use the `management.endpoints.web.exposure.exclude` property as below.

~~~properties
management.endpoints.web.exposure.exclude=*
~~~

![All HTTP Endpoints Excluded](https://github.com/kumar-github/tutorial-resources/assets/2657313/84153c3c-7a51-4b69-bba4-07321d4a7aa9)

### Hiding Individual HTTP Endpoints

To hide (not expose) individual endpoints over **HTTP**, use the `management.endpoints.web.exposure.exclude` property as below.

~~~properties
management.endpoints.web.exposure.exclude=health
~~~

![HTTP health Endpoint Excluded](https://github.com/kumar-github/tutorial-resources/assets/2657313/b793c50e-0905-45a6-8284-00e67f7a1f7b)

Both `management.endpoints.web.exposure.exclude` and `management.endpoints.web.exposure.include` properties can be used together to have more fine-grained control over what to expose and what not to like below

```properties
management.endpoints.web.exposure.include=*
management.endpoints.web.exposure.exclude=beans,health,info
```

![HTTP Endpoints beans-health-info Excluded](https://github.com/kumar-github/tutorial-resources/assets/2657313/3910ea39-2a73-4106-927e-cad4d1832c71)

As `management.endpoints.web.exposure.exclude` has more priority than `management.endpoints.web.exposure.include`, the above configuration will expose all endpoints over **HTTP** and hides only the `beans`, `health`, `info` endpoints.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
