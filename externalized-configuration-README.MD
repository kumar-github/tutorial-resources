# Spring Boot Externalized Configuration 101

> **Every day I am learning, growing and thriving.**

This project will walk you through a simple demo of externalizing the configurations in a *Spring Boot* application .
It will help you to understand various approaches in externalizing the configurations in an application step by step.
Each concept is covered in-depth and organized as individual git commits. The commits are numbered sequentially starting
from `00`, `01`, `02` and so on. As you go through it, take your time to understand the changes made to the
code in each commit. In order to follow along, clone the entire project to your local machine, revert back to the
initial commit and then start applying the commits one by one starting from `00`. This `README.md` file will be updated
in every commit and will tell you what has been covered in the specific commit.

<br/>

---

---

---

<br/>

Table Of Contents
=================

* [Spring Boot Externalized Configuration 101](#spring-boot-externalized-configuration-101)
    * [Few things about Externalizing Configuration](#few-things-about-externalizing-configuration)
    * [Complete Reference](#complete-reference)
    * [Requirements](#requirements)
    * [Run Locally](#run-locally)
    * [Commit-00 :sparkles:](#commit-00-sparkles)
        * [Project Creation](#project-creation)
        * [Run the project from IDE](#run-the-project-from-ide)
        * [Run the project from command line using maven](#run-the-project-from-command-line-using-maven)
        * [Run the project from command line using java](#run-the-project-from-command-line-using-java)

<br/>

---

---

---

<br/>

## Few things about Externalizing Configuration

*Spring Boot* lets you externalize your configuration so that you can work with the same application code in different
environments. You can use a variety of external configuration sources including *properties files, YAML files,
environment variables, and command-line arguments*.

Property values can be injected directly into your beans by using the `@Value` annotation, accessed through Spring's
`Environment` abstraction, or be bound to structured objects through `@ConfigurationProperties`.

<br/>

---

---

---

<br/>

## Complete Reference

[Spring Boot Externalized Configuration Reference](https://docs.spring.io/spring-boot/reference/features/external-config.html)

<br/>

---

---

---

<br/>

## Requirements

* OpenJDK 21+
* Apache Maven 3.9.9+

<br/>

---

---

---

<br/>

## Run Locally

Clone the project

```bash
git clone https://github.com/kumar-github/spring-boot-externalized-configuration-101
```

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
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

| **Agenda for this commit**                           |      Covered?      |
|------------------------------------------------------|:------------------:|
| 1. Project creation with required dependencies.      | :white_check_mark: |
| 2. Run the project from IDE.                         | :white_check_mark: |
| 3. Run the project from command line using maven.    | :white_check_mark: |
| 4. Run the project from command line using java. | :white_check_mark: |

### Project Creation

We have created a spring boot project via [Spring Initializr](https://start.spring.io/) with below dependencies.

1. `spring-boot-starter-web`

### Run the project from IDE

Go the `Application.java` file and run the application from the IDE.

### Run the project from command line using maven

Since it is a maven project, the application can be run from command line using the below maven command.

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
./mvnw spring-boot:run
```
*Note: I am using the maven wrapper command to run the project.*

### Run the project from command line using java

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
./mvnw clean package
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```
*Note: Make sure java is on the path*

At this point, the application can be started and accessed on `http://localhost:8081`. Though accessing
`http://localhost:8081` will give you a `Whitelabel Error Page` but that is understandable since we did not map any
controller to handle the request.

But we can find the below line logged in the console which is the proof that actuator is enabled.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
