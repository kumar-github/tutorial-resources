> [!NOTE]  
> Highlights information that users should take into account, even when skimming.

> [!TIP]
> Optional information to help a user be more successful.

> [!IMPORTANT]  
> Crucial information necessary for users to succeed.

> [!WARNING]  
> Critical content demanding immediate user attention due to potential risks.

> [!CAUTION]
> Negative potential consequences of an action.

<br/>

---

---

---

<br/>

# Spring Boot Externalized Configuration 101

> **Every day I am learning, growing and thriving.**

This project will walk you through a simple demo of externalizing the configurations in a *Spring Boot* application .
It will help you to understand various approaches in externalizing the configurations in an application step by step.
Each concept is covered in-depth and organized as individual git commits. The commits are numbered sequentially starting
from `00`, `01`, `02` and so on. As you go through it, take your time to understand the changes made to the
code in each commit. In order to follow along, clone the repository to your local machine, revert back to the
initial commit and then start applying the commits one by one starting from `00`. This `README.MD` file will be updated
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
        * [Project creation with required dependencies](#project-creation-with-required-dependencies)
        * [Run the project from IDE](#run-the-project-from-ide)
        * [Run the project from command line using maven](#run-the-project-from-command-line-using-maven)
        * [Run the project from command line using java](#run-the-project-from-command-line-using-java)
    * [Commit-01 :sparkles:](#commit-01-sparkles)
        * [Add custom properties in application properties file](#add-custom-properties-in-application-properties-file)
        * [Access the custom properties in code using value annotation](#access-the-custom-properties-in-code-using-value-annotation)

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

Clone the project either via HTTPS or SSH

Using HTTPS

```bash
git clone https://github.com/kumar-github/spring-boot-externalized-configuration-101.git
```

![clone using https](https://github.com/user-attachments/assets/d96cea39-d852-43d0-8575-6a7bcddc010e)

Using SSH

```bash
git clone git@github.com:kumar-github/spring-boot-externalized-configuration-101.git
```

![clone using https](https://github.com/user-attachments/assets/ff8df5e4-b89d-493f-a6d3-e95250ffc988)

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Start the service

```bash
./mvnw spring-boot:run
```

![run using maven](https://github.com/user-attachments/assets/b7439361-ae1b-4d71-bfec-c67a9dc66a33)

<br/>

---

---

---

<br/>

## Commit-00 :sparkles:

*This is the Initial Commit.*

| **Agenda for this commit**                        |      Covered?      |
|---------------------------------------------------|:------------------:|
| 1. Project creation with required dependencies.   | :white_check_mark: |
| 2. Run the project from IDE.                      | :white_check_mark: |
| 3. Run the project from command line using maven. | :white_check_mark: |
| 4. Run the project from command line using java.  | :white_check_mark: |

### Project creation with required dependencies

We have created a spring boot project via [Spring Initializr](https://start.spring.io/) with below dependencies.

1. `spring-boot-starter-web`

### Run the project from IDE

Open the project in your favourite IDE and go to the `Application.java` file and run the application.

![run from ide](https://github.com/user-attachments/assets/07a5101b-02a3-401e-acc9-6d4d7531403f)

### Run the project from command line using maven

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Run the application

```bash
./mvnw spring-boot:run
```

![run using maven](https://github.com/user-attachments/assets/b7439361-ae1b-4d71-bfec-c67a9dc66a33)

> [!NOTE]
> I am using the maven wrapper command to run the project.

### Run the project from command line using java

Go to the project root directory

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Generate the jar

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/e3f7eb40-f709-4b03-b4d3-80791a93fedd)

Run the application

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java](https://github.com/user-attachments/assets/70f66807-b340-44a3-b269-8452f38e4a7d)

> [!NOTE]
> Make sure java is on the path.

At this point, the application can be started and accessed on `http://localhost:8081`. Though accessing
`http://localhost:8081` will give you the `Whitelabel Error Page` but that is understandable since we did not map any
controller to handle the request.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-01 :sparkles:

| **Agenda for this commit**                                      |      Covered?      |
|-----------------------------------------------------------------|:------------------:|
| 1. Add custom properties in application properties file.        | :white_check_mark: |
| 2. Access the custom properties in code using value annotation. | :white_check_mark: |

### Add custom properties in application properties file

We already have two built-in spring properties `spring.application.name, server.port` in our `application.properties`
file. In order to understand it better, we also added a new custom property named `app.username` to the
`application.properties` file. We also added a `HelloController` class from which we will access all the properties. In
the next step we will use the `@Value` annotation in the controller class to access all the properties from the
`application.properties` file.

### Access the custom properties in code using value annotation

In the `HelloController` class we have declared three fields `appName`, `serverPort`, `username` and a method
`appDetails` which will return the values of all these declared fields. We used the `@Value` annotation on each
field to inject the value from the `application.properties` file. The `@Value` annotation will look for the
mentioned property in any of the property sources (at the moment the `application.properties` file) and inject the
value into the field if the property exists or throws an exception at the application start-up if the property does
not exists and no default value provided. The syntax of `@Value` annotation is `@Value("${property-name}")` where
the property-name is the name of the property from `application.properties` file or from any other property sources.
In our case, the values of the below three properties are injected into the respective fields in the `HelloController`
class because of the `@Value` annotation.

```properties
spring.application.name = spring-boot-externalized-configuration-101
server.port             = 8081
app.username            = admin
```

> [!TIP]
> It is a convention to follow the `.` notation for property names and group them based on their functionality.
>
> Example: `app.database.url`, `app.database.driver-class-name`, `app.database.security.username` etc


:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

---
---
---
---
---
---

![clone using https](https://github.com/user-attachments/assets/d96cea39-d852-43d0-8575-6a7bcddc010e)
<br><br>
![clone using https](https://github.com/user-attachments/assets/ff8df5e4-b89d-493f-a6d3-e95250ffc988)
<br><br>
![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)
<br><br>
![run using maven](https://github.com/user-attachments/assets/b7439361-ae1b-4d71-bfec-c67a9dc66a33)
<br><br>
![maven clean package](https://github.com/user-attachments/assets/e3f7eb40-f709-4b03-b4d3-80791a93fedd)
<br><br>
![run from ide](https://github.com/user-attachments/assets/07a5101b-02a3-401e-acc9-6d4d7531403f)
<br><br>
![run using java](https://github.com/user-attachments/assets/70f66807-b340-44a3-b269-8452f38e4a7d)
