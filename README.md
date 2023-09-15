# Spring Boot Actuator
> **Every day is a learning day.**

This project will help you in understanding **Spring Boot Actuator** module step by step. Each concept is covered in-depth and organized as individual git commits. The commits are numbered sequentially starting from `00`, `01`, `02` and so on. You can clone the entire project to your local machine and then start applying commits one by one starting from `00`. This `README.md` file will be updated in every commit and will tell you what has been covered in the specific commit.

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

## Commit-00 :sparkles:

*This is the Initial Commit.*

| **Agenda for this commit**                                    | Covered?           |
| ----------------------------------------------                | :--:               |
| 1. Project creation with needed dependencies.                 | :white_check_mark: |
| 2. Observe actuator related console log.                      | :white_check_mark: |
| 3. Hit the actuator endpoints over **HTTP**.                  | :white_check_mark: |
| 4. Talk about `Health` endpoint.                              | :white_check_mark: |
| 5. Access the actuator endpoints over **JMX** using jconsole. | :white_check_mark: |
| 6. Talk about `Shutdown` endpoint.                            | :white_check_mark: |
| 7. Talk briefly about `Info` endpoint.                        | :white_check_mark: |

We have created a spring boot project via [Spring Initializr](https://start.spring.io/) with below dependencies.

1. `spring-boot-starter-web`
2. `spring-boot-starter-actuator`
3. `spring-boot-devtools`

*Note: `spring-boot-devtools` is not mandatory but i am using here because of the live reload feature which will save some development time.*

At this point, the application can be started and accessed on `http://localhost:9090`. Though accessing `http://localhost:9090` will give you a `Whitelabel Error Page` but that is understandable since we did not map any controller to handle the request.

But we can find the below line logged in the console which is the proof that actuator is enabled.

```console
Exposing 1 endpoint(s) beneath base path '/actuator'
```

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

![Endpoints Over JMX](https://github.com/kumar-github/tutorial-resources/assets/2657313/fa21802c-67d7-4a9c-a66f-415e7a896ecb)


You can access the individual endpoints like `beans`, `health`, `info` etc here.

*Note: The `shutdown` endpoint is also exposed here by default. But in **HTTP** we need to enable and expose it manually.*

*Note: Spend some time exploring few endpoints like `beans`, `health`, `info` etc.*

*Note: The `info` endpoint does not return any information at the moment but we are going to fix it soon.*

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
