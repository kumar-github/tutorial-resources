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
    * [Commit-01 :sparkles:](#commit-01-sparkles)
        * [Add custom properties in application properties file](#add-custom-properties-in-application-properties-file)
        * [Access the custom properties using @Value annotation](#access-the-custom-properties-using-value-annotation)
    * [Commit-02 :sparkles:](#commit-02-sparkles)
        * [Access the properties using the Environment abstraction](#access-the-properties-using-the-environment-abstraction)
        * [The Environment abstraction](#the-environment-abstraction)
    * [Commit-03 :sparkles:](#commit-03-sparkles)
        * [Overriding the properties using an external application properties file](#overriding-the-properties-using-an-external-application-properties-file)
        * [Run the project from command line using java with the internal application properties file](#run-the-project-from-command-line-using-java-with-the-internal-application-properties-file)
        * [Run the project from command line using maven with the internal application properties file](#run-the-project-from-command-line-using-maven-with-the-internal-application-properties-file)
        * [Run the project from command line using java with an external application properties file](#run-the-project-from-command-line-using-java-with-an-external-application-properties-file)
        * [Run the project from command line using maven with an external application properties file](#run-the-project-from-command-line-using-maven-with-an-external-application-properties-file)

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

<img src="https://github.com/user-attachments/assets/7bf6ed23-1b24-4cc9-9762-65fe29c80240" alt="git clone using
https" title="git clone using https" width="1050">

Using SSH.

```bash
git clone git@github.com:kumar-github/spring-boot-externalized-configuration-101.git
```

<img src="https://github.com/user-attachments/assets/a8396741-ab1d-4e81-a383-c218fa3a56ef" alt="git clone using ssh" title="git clone using ssh" width="1050">

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Run the application.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="maven spring boot run simple" title="maven spring boot run simple" width="1050">

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

Open the project in your favourite IDE, go to the `Application.java` file and run the application by clicking the gutter
icons.

<img src="https://github.com/user-attachments/assets/672a6194-2f0a-4e67-b79a-08621484397f" alt="run from ide" title="run from ide" width="1050">

### Run the project from command line using maven

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Run the application.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="run using maven" title="run using maven" width="1050">

> [!NOTE]
> I am using the maven wrapper command to run the project.

### Run the project from command line using java

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Generate the jar.

```bash
./mvnw clean package
```

<img src="https://github.com/user-attachments/assets/3ff834d2-8454-4fda-a12d-07959ba1b724" alt="maven clean package" title="maven clean package" width="1050">

Target folder with jar generated.

<img src="https://github.com/user-attachments/assets/504931aa-6930-4748-b7d2-19273dd45a1c" alt="ls and ls target folder" title="ls and ls target folder" width="1050">

Run the application.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

<img src="https://github.com/user-attachments/assets/b1a577d9-4a7c-4818-b23b-640dc90b0186" alt="run using java" title="run using java" width="1050">

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

| **Agenda for this commit**                               |      Covered?      |
|----------------------------------------------------------|:------------------:|
| 1. Add custom properties in application properties file. | :white_check_mark: |
| 2. Access the custom properties using @Value annotation. | :white_check_mark: |

### Add custom properties in application properties file

We already have two built-in spring properties `spring.application.name, server.port` in our `application.properties`
file. In order to understand it better, we also added a new custom property named `app.username` to the
`application.properties` file. We also added a `HelloController` class from which we will access all the properties. In
the next step we will use the `@Value` annotation in the controller class to access all the properties from the
`application.properties` file.

### Access the custom properties using @Value annotation

In the `HelloController` class we have declared three fields `appName`, `serverPort`, `username` and a method
`appDetails` which will return the concatenated values of all these declared fields. We used the `@Value` annotation
on each field to inject the value from the `application.properties` file. The `@Value` annotation will look for the
mentioned property in any of the property sources (at the moment the `application.properties` file) and inject the value
into the field if the property exists or throws an exception at the application start-up if the property does not exists
and no default value provided. The syntax of `@Value` annotation is `@Value("${property-name}")` where the
property-name is the name of the property from `application.properties` file or from any other property sources. In
our case, the values of the below three properties are injected into the respective fields in the `HelloController`
class using the `@Value` annotation.

```properties
spring.application.name = spring-boot-externalized-configuration-101
server.port             = 8081
app.username            = admin
```

> [!TIP]
> It is a convention to follow the `.` and lower case notation for property names and group them based on their
> functionality.
>
> Example: `app.database.url`, `app.database.driver-class-name`, `app.database.security.username` etc

Run the application and hit the below endpoint.

`http://localhost:8081/appDetails`

We can see the below response.

<img src="https://github.com/user-attachments/assets/b688b7c3-1235-4ae4-b9d7-e0733e53662b" alt="app details endpoint" title="app details endpoint" width="1050">

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-02 :sparkles:

| **Agenda for this commit**                                  |      Covered?      |
|-------------------------------------------------------------|:------------------:|
| 1. Access the properties using the Environment abstraction. | :white_check_mark: |
| 2. The Environment abstraction.                             | :white_check_mark: |

### Access the properties using the Environment abstraction

In the previous commit, we have used `@Value` annotation to access the properties from the `application.properties`
file. In this commit, we will use Spring's `Environment` abstraction to do the same. We also added a new custom property
named `app.password` to the `application.properties` file. In the `HelloController` class, we have removed all the
`@Value` annotated fields and instead we used the `Environment` abstraction's `getProperty(...)` method to get the
value.

```java
    @GetMapping("/appDetails")
    public String appDetails() {
        final String appName    = environment.getProperty("spring.application.name");
        final String serverPort = environment.getProperty("server.port");
        final String username   = environment.getProperty("app.username");
        final String password   = environment.getProperty("app.password");
        return appName + " : " + serverPort + " : " + username + " : " + password;
    }
```

Run the application and hit the below endpoint.

`http://localhost:8081/appDetails`

We can see the below response.

<img src="https://github.com/user-attachments/assets/9f1347ef-d023-4f03-95ce-3f644fea9963" alt="app details endpoint using environment abstraction" title="app details endpoint using environment abstraction" width="1050">

### The Environment abstraction

The `Environment` interface is a perfect example for abstraction. It is integrated in the container that models two key
aspects of the application environment: `profiles` and `properties`. Here we only talk about the `properties`.
Properties may originate from a variety of sources: properties files, JVM system properties, system environment
variables, JNDI, servlet context parameters, ad-hoc Properties objects, Map objects, and so on. The role of the
`Environment` object with relation to properties is to provide the user with a convenient service interface for
configuring property sources and resolving properties from them. You can use the `Environment` interface to read
properties literally from any property sources listed above. As a user, you don't have to worry about the underlying
source from which a property is coming from.

Spring’s Environment abstraction provides search operations over a configurable hierarchy of property sources. Consider
the following code snippet:

```java
@Autowired
private Environment environment;

...

boolean containsMyProperty = environment.containsProperty("my-property");
System.out.println("Does my environment contain the 'my-property' property? " + containsMyProperty);
```

In the preceding snippet, we see a high-level way of asking Spring whether the `my-property` property is defined for the
current environment. To answer this question, the `Environment` object performs a search over a set of `PropertySource`
objects. A `PropertySource` is a simple abstraction over any source of key-value pairs, and Spring’s
`StandardEnvironment` is configured with two `PropertySource` objects.

- one representing the set of JVM system properties. (`System.getProperties()`)
- one representing the set of system environment variables. (`System.getenv()`)

> [!TIP]
> The search performed is hierarchical. By default, system properties have precedence over environment variables. So, if
> the `my-property` property happens to be set in both places during a call to `environment.getProperty("my-property")`,
> the system property value **wins** and is returned.

> [!NOTE]
> Note that property values are not merged but rather completely overridden by a succeeding entry.

The following figure shows how the Spring environment is created from the available configuration property sources.

<img src="https://github.com/user-attachments/assets/64e922ce-2e48-4c7e-990c-2f7ab7cf8f14" alt="spring configuration generated from java property sources" title="spring configuration generated from java property sources" width="1050">

> [!TIP]
> The entire mechanism is configurable. If you have a custom source of properties that you want to integrate into
> the `Environment`'s search, you can do so by implementing and instantiating your own `PropertySource` and add it to
> the set of `PropertySources` for the current `Environment`.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-03 :sparkles:

| **Agenda for this commit**                                                                      |      Covered?      |
|-------------------------------------------------------------------------------------------------|:------------------:|
| 1. Overriding the properties using an external application properties file.                     | :white_check_mark: |
| 2. Run the project from command line using java with the internal application properties file.  | :white_check_mark: |
| 3. Run the project from command line using maven with the internal application properties file. | :white_check_mark: |
| 4. Run the project from command line using java with an external application properties file.   | :white_check_mark: |
| 5. Run the project from command line using maven with an external application properties file.  | :white_check_mark: |

### Overriding the properties using an external application properties file

On the application classpath (for example, inside the jar) currently we have an `application.properties` file
that provides sensible default values for properties `spring.application.name`, `server.port`, `app.username` and
`app.password`. But when running in a new environment, an `application.properties` file can be provided outside of the
jar that will override the values of the above mentioned properties.

### Run the project from command line using java with the internal application properties file

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Generate the jar.

```bash
./mvnw clean package
```

<img src="https://github.com/user-attachments/assets/3ff834d2-8454-4fda-a12d-07959ba1b724" alt="maven clean package" title="maven clean package" width="1050">

Target folder with jar generated.

<img src="https://github.com/user-attachments/assets/504931aa-6930-4748-b7d2-19273dd45a1c" alt="ls and ls target folder" title="ls and ls target folder" width="1050">

Run the application using java jar command.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

<img src="https://github.com/user-attachments/assets/b1a577d9-4a7c-4818-b23b-640dc90b0186" alt="run using java" title="run using java" width="1050">

Check the port on the console.

<img src="https://github.com/user-attachments/assets/3d87116d-f1d4-4365-bc1b-6950b72819bd" alt="app started from console" title="app started from console" width="1050">

Check the response on the browser.

<img src="https://github.com/user-attachments/assets/9f1347ef-d023-4f03-95ce-3f644fea9963" alt="app details endpoint" title="app details endpoint" width="1050">

> [!NOTE]
> The application uses the `application.properties` file from inside the jar and starts on port 8081. The values for
> other properties also picked up from the same `application.properties` file (inside the jar).

### Run the project from command line using maven with the internal application properties file

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Run the application using maven command.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="run using maven" title="run using maven" width="1050">

Check the port on the console.

<img src="https://github.com/user-attachments/assets/b0c94d3a-ad09-4ed5-b085-72650313ef9c" alt="app started from console" title="app started from console" width="1050">

Check the response on the browser.

<img src="https://github.com/user-attachments/assets/9f1347ef-d023-4f03-95ce-3f644fea9963" alt="app details endpoint" title="app details endpoint" width="1050">

> [!NOTE]
> The application uses the `application.properties` file from inside the jar and starts on port 8081. The values for
> other properties also picked up from the same `application.properties` file (inside the jar).

### Run the project from command line using java with an external application properties file

Create a new `application.properties` file with different values for the properties and place it in the project root
directory.

<img src="https://github.com/user-attachments/assets/512522da-782f-497c-a6d0-756abcaadbff" alt="added application properties file externally" title="added application properties file externally" width="1050">

> [!TIP]
> This `application.properties` file can be placed anywhere (external to the project) but you need to make sure that
> the command to run the application should be issued from the folder where the `application.propertiles` file exists.

Run the application using java jar command.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

<img src="https://github.com/user-attachments/assets/b1a577d9-4a7c-4818-b23b-640dc90b0186" alt="run using java" title="run using java" width="1050">

Check the port on the console.

<img src="https://github.com/user-attachments/assets/1021e0dc-a10e-46d3-ace6-38a2238175ce" alt="app started from console" title="app started from console" width="1050">

Check the response on the browser.

<img src="https://github.com/user-attachments/assets/9861c308-1bcb-4c8c-8e16-336496a2f4b1" alt="app details endpoint" title="app details endpoint" width="1050">

> [!NOTE]
> Now the application uses the external `application.properties` file and starts on port 8082. The values for other
> properties also picked up from the same `application.properties` file (external to the jar).

### Run the project from command line using maven with an external application properties file

Follow the above steps to create a new `application.properties` file and place it external to the jar.

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width="1050">

Run the application using maven command.

```bash
./mvnw spring-boot:run
```

<img src="https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d" alt="maven spring boot run simple" title="maven spring boot run simple" width="1050">

Check the port on the console.

<img src="https://github.com/user-attachments/assets/58edb65b-e6c7-411a-82b2-8b77635767d1" alt="app started from console" title="app started from console" width="1050">

Check the response on the browser.

<img src="https://github.com/user-attachments/assets/9861c308-1bcb-4c8c-8e16-336496a2f4b1" alt="app details endpoint" title="app details endpoint" width="1050">

> [!NOTE]
> Now the application uses the external `application.properties` file and starts on port 8082. The values for other
> properties also picked up from the same `application.properties` file (external to the jar).

> [!TIP]
> Make sure to run the application from the folder where the external `application.properties` file is placed. In
> our example the `application.properties` file is placed in the project root folder and the command to start the
> application is executed from the same folder.

> [!WARNING]
> We copied the same `application.properties` file inside the target folder. In this case, the commands should be
> executed from within the target folder, otherwise the `application.properties` file will NOT be picked up.
>
> Check the below screenshot.

<img src="https://github.com/user-attachments/assets/80066648-676d-4b56-b341-7464eba48c99" alt="application properties file inside target folder" title="application properties file inside target folder" width="1050">

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>
