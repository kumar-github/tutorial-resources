# Spring Boot Externalized Configuration 101

> **Every day I am learning, growing and thriving.**

This project will walk you through a simple demo of externalizing the configurations in a *Spring Boot* application .
It will help you to understand various approaches in externalizing the configurations in an application step by step.
Each concept is covered in-depth and organized as individual git commits. The commits are numbered sequentially starting
from `00`, `01`, `02` and so on. As you go through it, take your time to understand the changes made to the
code in each commit. In order to follow along, clone the repository to your local machine, revert back to the
initial commit and then start applying the commits one by one starting from `00`. This `README.MD` file will be updated
in every commit and will tell you what has been covered in the specific commit.

---

---

---

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

---

---

---

## Few things about Externalizing Configuration

*Spring Boot* lets you externalize your configuration so that you can work with the same application code in different
environments. You can use a variety of external configuration sources including *properties files, YAML files,
environment variables, and command-line arguments*.

Property values can be injected directly into your beans by using the `@Value` annotation, accessed through Spring's
`Environment` abstraction, or be bound to structured objects through `@ConfigurationProperties`.

---

---

---

## Complete Reference

[Spring Boot Externalized Configuration Reference](https://docs.spring.io/spring-boot/reference/features/external-config.html)

---

---

---

## Requirements

* OpenJDK 21+
* Apache Maven 3.9.9+

---

---

---

## Run Locally

Clone the project either via HTTPS or SSH.

Using HTTPS.

```bash
git clone https://github.com/kumar-github/spring-boot-externalized-configuration-101.git
```

<img src="https://github.com/user-attachments/assets/7bf6ed23-1b24-4cc9-9762-65fe29c80240" alt="git clone using https" title="git clone using https" width="1050"/>

Using SSH.

```bash
git clone git@github.com:kumar-github/spring-boot-externalized-configuration-101.git
```

<img src="https://github.com/user-attachments/assets/a8396741-ab1d-4e81-a383-c218fa3a56ef" alt="git clone using ssh" title="git clone using ssh" width="1050"/>

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050"/>

Run the application.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="maven spring boot run simple" title="maven spring boot run simple" width="1050"/>

---

---

---

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

<img src="https://github.com/user-attachments/assets/07a5101b-02a3-401e-acc9-6d4d7531403f" alt="run from ide" title="run from ide" width="1050"/>

### Run the project from command line using maven

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050"/>

Run the application.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="run using maven" title="run using maven" width="1050"/>

> [!NOTE]
> I am using the maven wrapper command to run the project.

### Run the project from command line using java

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050"/>

Generate the jar.

```bash
./mvnw clean package
```

<img src="https://github.com/user-attachments/assets/3ff834d2-8454-4fda-a12d-07959ba1b724" alt="maven clean package" title="maven clean package" width="1050"/>

Target folder with jar generated.

<img src="https://github.com/user-attachments/assets/504931aa-6930-4748-b7d2-19273dd45a1c" alt="ls and ls target folder" title="ls and ls target folder" width="1050"/>

Run the application.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

<img src="https://github.com/user-attachments/assets/b1a577d9-4a7c-4818-b23b-640dc90b0186" alt="run using java" title="run using java" width="1050"/>

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
