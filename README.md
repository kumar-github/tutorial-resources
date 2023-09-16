## Commit-02:sparkles:

| **Agenda for this commit**                            |      Covered?      |
| -------------------------------------------------     | :----------------: |
| 1. Enabling All Endpoints.                            | :white_check_mark: |
| 2. Enabling Individual Endpoints (`shutdown`).        | :white_check_mark: |
| 3. Talk briefly about CONDITION EVALUATION DELTA.     | :white_check_mark: |
| 4. Disabling All Endpoints.                           | :white_check_mark: |
| 5. Disabling Individual Endpoints (`health`, `info`). | :white_check_mark: |

### Enabling/Disabling Endpoints

#### Enabling All Endpoints

By default all management endpoints are **enabled** (except `shutdown`). You don't have to do anything extra to enable them.

#### Enabling Individual Endpoints
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

#### Disabling All Endpoints

If you want to disable all the endpoints, you can use the `management.endpoints.enabled-by-default` property as below.

~~~properties
management.endpoints.enabled-by-default=false
~~~

*Note: A **disabled** endpoint will not be exposed neither over **JMX** nor over **HTTP**.*

#### Disabling Individual Endpoints

If you want to disable an individual endpoint, you can use the respective `management.endpoint.<ENDPOINT_ID>.enabled` property as below.

~~~properties
management.endpoint.health.enabled=false
management.endpoint.info.enabled=false
~~~

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
