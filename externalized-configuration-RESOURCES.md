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

<br/><br/><br/><br/>
<img src="https://github.com/user-attachments/assets/7bf6ed23-1b24-4cc9-9762-65fe29c80240" alt="git clone using https" title="git clone using https" width=1000/>
<br/><br>
<img src="https://github.com/user-attachments/assets/a8396741-ab1d-4e81-a383-c218fa3a56ef" alt="git clone using ssh" title="git clone using ssh" width=1000/>
<br/><br>
<img src="https://github.com/user-attachments/assets/06ce1c18-187d-4039-a0d1-150e1ab7a7d6" alt="ls" title="ls" width=1000/>
<br/><br>
<img src="https://github.com/user-attachments/assets/0affda3b-f1a2-45bb-bfb4-697148b77f0a" alt="change directory and ls without target folder" title="change directory and ls without target folder" width=1000/>
<br/><br>
![change directory and ls with target folder-big](https://github.com/user-attachments/assets/fde36629-2c5f-4f66-98ae-74dd73cb0c54 "change directory and ls with target folder-big")
<br/><br>
![ls target folder-small](https://github.com/user-attachments/assets/bc554deb-212b-4e22-85b4-75b47c5f24c8 "ls target folder-small")
<br/><br>
![ls target folder-big](https://github.com/user-attachments/assets/da67e56a-e1f0-483a-89a4-4f4d458da30e "ls target folder-big")
<br/><br>
![ls and ls target folder-big](https://github.com/user-attachments/assets/504931aa-6930-4748-b7d2-19273dd45a1c "ls and ls target folder-big")
<br/><br>
![maven clean package](https://github.com/user-attachments/assets/3ff834d2-8454-4fda-a12d-07959ba1b724 "maven clean package")
<br/><br>
![maven spring boot run simple](https://github.com/user-attachments/assets/f3620e20-1eaa-4137-af64-f78e041da83d "maven spring boot run simple")
<br/><br>
![run from ide](https://github.com/user-attachments/assets/07a5101b-02a3-401e-acc9-6d4d7531403f)
<br/><br>
![java jar run simple](https://github.com/user-attachments/assets/b1a577d9-4a7c-4818-b23b-640dc90b0186 "java jar run simple")
<br/><br>
![java jar run with system properties](https://github.com/user-attachments/assets/6e5b7c02-f933-4820-a506-bd538c9d99e3 "java jar run with system properties")
<br/><br>
![java jar run with command line args](https://github.com/user-attachments/assets/2e32fa41-47bb-4348-8a22-f2a4fe5607fa "java jar run with command line args")
<br/><br>
![maven spring boot run with arguments](https://github.com/user-attachments/assets/1fa9b2b9-8029-4ed9-85bc-acd362137fb3 "maven spring boot run with arguments")
<br/><br>
![maven spring boot run with spring config name](https://github.com/user-attachments/assets/af2ddba1-171c-4f12-a4e2-41ca053fcd75 "maven spring boot run with spring config name")
<br/><br><br/><br>


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
    * [Commit-04 :sparkles:](#commit-04-sparkles)
        * [Overriding the properties using environment variables](#overriding-the-properties-using-environment-variables)
        * [Binding from environment variables](#binding-from-environment-variables)
    * [Commit-05 :sparkles:](#commit-05-sparkles)
        * [Overriding the properties using command line arguments](#overriding-the-properties-using-command-line-arguments)
    * [Commit-06 :sparkles:](#commit-06-sparkles)
        * [Changing the default configuration file](#changing-the-default-configuration-file)

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

Clone the project either via HTTPS or SSH.

Using HTTPS.

```bash
git clone https://github.com/kumar-github/spring-boot-externalized-configuration-101.git
```

![clone using https](https://github.com/user-attachments/assets/d96cea39-d852-43d0-8575-6a7bcddc010e)

Using SSH.

```bash
git clone git@github.com:kumar-github/spring-boot-externalized-configuration-101.git
```

![clone using https](https://github.com/user-attachments/assets/ff8df5e4-b89d-493f-a6d3-e95250ffc988)

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Start the service.

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

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Run the application.

```bash
./mvnw spring-boot:run
```

![run using maven](https://github.com/user-attachments/assets/b7439361-ae1b-4d71-bfec-c67a9dc66a33)

> [!NOTE]
> I am using the maven wrapper command to run the project.

### Run the project from command line using java

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101
```

![change directory](https://github.com/user-attachments/assets/6d4e9450-a2fa-4619-ae26-dc67a07ab84d)

Generate the jar.

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/e3f7eb40-f709-4b03-b4d3-80791a93fedd)

Run the application.

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

![app details endpoint](https://github.com/user-attachments/assets/7dcea6df-ae02-498b-beb7-36289376a2d1)

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

![app details endpoint using environment abstraction](https://github.com/user-attachments/assets/7ab09997-0615-4453-8f9a-f232c96ec6dd)

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

![spring configuration generated from java property sources](https://github.com/user-attachments/assets/64e922ce-2e48-4c7e-990c-2f7ab7cf8f14)

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

![change directory](https://github.com/user-attachments/assets/a69d50fe-2091-470d-84da-9faf4338b575)

Generate the jar.

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/93540c9a-865b-4a90-bcd1-79c1baaad901)

Target folder generated with jar.

![target folder and jar generated](https://github.com/user-attachments/assets/e9853a60-559b-4d85-ac3d-416c2b7ac229)

Run the application using java jar command.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java](https://github.com/user-attachments/assets/44267789-2b19-4ba5-9b95-ccfbe3f32d20)

Check the port on the console.

![app started from console](https://github.com/user-attachments/assets/3d87116d-f1d4-4365-bc1b-6950b72819bd)

Check the response on the browser.

![app details endpoint](https://github.com/user-attachments/assets/ac5ce80d-c417-4b37-8509-3872c0875501)

> [!NOTE]
> The application uses the `application.properties` file from inside the jar and starts on port 8081. The values for
> other properties also picked up from the same `application.properties` file (inside the jar).

### Run the project from command line using maven with the internal application properties file

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

![change directory](https://github.com/user-attachments/assets/a69d50fe-2091-470d-84da-9faf4338b575)

Run the application using maven command.

```bash
./mvnw spring-boot:run
```

![maven run](https://github.com/user-attachments/assets/3d21ec95-4866-41ca-be18-fb810d0a49d3)

Check the port on the console.

![app started from console](https://github.com/user-attachments/assets/b0c94d3a-ad09-4ed5-b085-72650313ef9c)

Check the response on the browser.

![app details endpoint](https://github.com/user-attachments/assets/ac5ce80d-c417-4b37-8509-3872c0875501)

> [!NOTE]
> The application uses the `application.properties` file from inside the jar and starts on port 8081. The values for
> other properties also picked up from the same `application.properties` file (inside the jar).

### Run the project from command line using java with an external application properties file

Create a new `application.properties` file with different values for the properties and place it in the project root
directory.

![added application properties file externally](https://github.com/user-attachments/assets/512522da-782f-497c-a6d0-756abcaadbff)

> [!TIP]
> This `application.properties` file can be placed anywhere (external to the project) but you need to make sure that
> the command to run the application should be issued from the folder where the `application.propertiles` file exists.

Run the application using java jar command.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java](https://github.com/user-attachments/assets/d1908e31-8d19-4767-b995-495b1d2575fb)

Check the port on the console.

![app started from console](https://github.com/user-attachments/assets/1021e0dc-a10e-46d3-ace6-38a2238175ce)

Check the response on the browser.

![app details endpoint](https://github.com/user-attachments/assets/9861c308-1bcb-4c8c-8e16-336496a2f4b1)

> [!NOTE]
> Now the application uses the external `application.properties` file and starts on port 8082. The values for other
> properties also picked up from the same `application.properties` file (external to the jar).

### Run the project from command line using maven with an external application properties file

Follow the above steps to create a new `application.properties` file and place it external to the jar.

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

![change directory](https://github.com/user-attachments/assets/a69d50fe-2091-470d-84da-9faf4338b575)

Run the application using maven command.

```bash
./mvnw spring-boot:run
```

![run using maven](https://github.com/user-attachments/assets/44e97ba2-5a9f-40cc-940b-7ea14f65148e)

Check the port on the console.

![app started from console](https://github.com/user-attachments/assets/58edb65b-e6c7-411a-82b2-8b77635767d1)

Check the response on the browser.

![app details endpoint](https://github.com/user-attachments/assets/9861c308-1bcb-4c8c-8e16-336496a2f4b1)

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

![application properties file inside target folder](https://github.com/user-attachments/assets/80066648-676d-4b56-b341-7464eba48c99)

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-04 :sparkles:

| **Agenda for this commit**                                |      Covered?      |
|-----------------------------------------------------------|:------------------:|
| 1. Overriding the properties using environment variables. | :white_check_mark: |
| 2. Binding from environment variables.                    | :white_check_mark: |

### Overriding the properties using environment variables

In the previous section, we have discussed about overriding the properties using an external `application.properties`
file. In this section we will see, how to override the properties via environment variables.

Make sure environment variables are NOT set.

![multiple line echo before setting env variables](https://github.com/user-attachments/assets/ffde260a-4fed-4199-bac9-42f3726e1e5c)

Run the application from command line using either java or maven.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

or

```bash
./mvnw spring-boot:run
```

Check the response on the browser.

![app details endpoint on browser before setting env variables](https://github.com/user-attachments/assets/d2d947ff-514c-443b-96a2-7c07a155ac3a)

> [!NOTE]
> The application injects the properties from the `application.properties` file from inside the jar.
> The application starts on port 8081 and the values for other properties also picked up from the same
> `application.properties` file (inside the jar).

Set the environment variables as per your OS.

![multiple line export](https://github.com/user-attachments/assets/0cde4ea6-668f-41ac-b4ac-9876d7dcceec)

> [!TIP]
> You can set multiple environment variables at once. Check the below screenshot.
>
> ![single line export](https://github.com/user-attachments/assets/10f22d9c-38fa-4117-b3be-4669b8fb38dc)

Check if the environment variables reflects your values.

![multiple line echo](https://github.com/user-attachments/assets/a30b19a9-5b0a-4fd5-8761-31c9eababde3)

> [!TIP]
> You can echo multiple environment variables at once. Check the below screenshot.
>
> ![single line echo](https://github.com/user-attachments/assets/f7529f44-a2bd-435c-b35a-ebf97a6df91e)

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

![change directory](https://github.com/user-attachments/assets/b989181b-2dbe-4860-bde1-b57501fd681a)

Generate the jar.

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/7cc05b60-1fcc-4e34-a1a0-c1b8d97f2020)

Run the application using java OR maven.

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java](https://github.com/user-attachments/assets/59136705-6009-488b-8575-a6429a1d391d)

```bash
./mvnw spring-boot:run
```

![run using maven](https://github.com/user-attachments/assets/d84345b3-b9c1-48f9-8347-3183db1a90c9)

Check the response on the browser.

![app details endpoint on browser](https://github.com/user-attachments/assets/22ac35c2-b33a-418d-8042-d396fd19cc4e)

> [!NOTE]
> Now the application uses the environment variables to inject the values for the properties. If the environment
> variables does not exists then it falls back to the `application.properties` file. The application starts on port 8083
> and the values for other properties also injected from the environment variables.

> [!NOTE]
> Even though we did not explicitly pass any of the properties to the application from command line, Spring is able
> to translate the environment variables to the correct application properties. This is because of the naming convention
> we followed for the environment variables.
> Example:
>
> `SPRING_APPLICATION_NAME` is translated to `spring.application.name`
>
> `SERVER_PORT` is translated to `server.port`
>
> `APP_USERNAME` is translated to `app.username`
>
> `APP_PASSWORD` is translated to `app.password`

> [!NOTE]
> It is just the OS's naming convention that the environment variables are in uppercase. Spring does not demand this.
> The application behaves exactly the same even if the environment variables are in lowercase as below.
>
> `spring_application_name`, `server_port`, `app_username`, `app_password`

> [!NOTE]
> In the `HelloController` class, we used both `@Value` annotation and the `Environment` abstraction to access the
> properties just for reference and to show that it works.

### Binding from environment variables

> [!IMPORTANT]
> Most operating systems impose strict rules around the names that can be used for environment variables. For example,
> Linux shell variables can contain only letters (a to z or A to Z), numbers (0 to 9) or the underscore character (_).
> By convention, Unix shell variables will also have their names in UPPERCASE.

Spring Boot’s relaxed binding rules are, as much as possible, designed to be compatible with these naming restrictions.
To convert a property name in the canonical-form to an environment variable name you can follow the below rules:

- Replace dots (.) with underscores (_).
- Remove any dashes (-).
- Convert to uppercase.

For example, the configuration property `spring.main.log-startup-info` would be an environment variable named
`SPRING_MAIN_LOGSTARTUPINFO`.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-05 :sparkles:

| **Agenda for this commit**                                 |      Covered?      |
|------------------------------------------------------------|:------------------:|
| 1. Overriding the properties using command line arguments. | :white_check_mark: |

### Overriding the properties using command line arguments

In the previous section, we have discussed about overriding the properties via environment variables. In this section we
will see, how to override the properties via command line arguments.

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

![change directory](https://github.com/user-attachments/assets/b989181b-2dbe-4860-bde1-b57501fd681a)

Generate the jar.

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/7cc05b60-1fcc-4e34-a1a0-c1b8d97f2020)

Run the application using java with command line arguments. [*Approach-1*]

```bash
java -Dspring.application.name=spring-boot-externalized-configuration-101-cl -Dserver.port=8084 -Dapp.username=admin-cl -Dapp.password=secret-cl -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java with command line-1](https://github.com/user-attachments/assets/997500be-5edc-43d1-9e42-e8875bc7c495)

Check the response on the browser.

![app details endpoint on browser using java with command line-1](https://github.com/user-attachments/assets/d5300fca-714d-40c9-b0af-ec85c8882871)

Run the application using java with command line arguments. [*Approach-2*]

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar --spring.application.name=spring-boot-externalized-configuration-101-cl --server.port=8085 --app.username=admin-cl --app.password=secret-cl
```

![run using java with command line-2](https://github.com/user-attachments/assets/e8d01ea6-094e-413d-a1dc-cc2a3975e1cc)

Check the response on the browser.

![app details endpoint on browser using java with command line-2](https://github.com/user-attachments/assets/68f8717c-aba4-4314-acd9-3c2ad040b31e)

> [!TIP]
> The `-D` switch in java allows you to pass a system property to the application as *key-value* pair. In plain java,
> you can use `System.getProperty(...)` method to access these properties.

> [!WARNING]
> `-D` should be passed before the `-jar` switch.

> [!IMPORTANT]
> Any values passed after the class or jar name will be considered as arguments to the *main* method and treated as
> plain strings(NOT interpreted as key-value pair). These arguments are just arbitrary strings and may or may not be
> prefixed with `--`. Java does not care whether it is prefixed with `--` or not and treat it as plain strings(NOT
> interpreted as key-value pair). If needed, you have to write your own logic to read those values and split them as
> key-value pairs.

> [!TIP]
> By default, Spring converts any system properties (prefixed with `-D`) and command line arguments that are
> prefixed with `--` to a Spring property and adds them to the `Environment` abstraction.
>
> If you do NOT want command line properties to be added to the `Environment`, you can disable them by using
> `SpringApplication.setAddCommandLineProperties(false)`.

> [!WARNING]
> If the arguments are NOT prefixed with `--`, Spring will not add it to the `Environment` abstraction.

Run the application using maven with command line arguments.

```bash
./mvnw spring-boot:run -Dspring-boot.run.arguments="--spring.application.name=spring-boot-externalized-configuration-101-cl --server.port=8086 --app.username=admin-cl --app.password=secret-cl"
```

![run using maven with command line](https://github.com/user-attachments/assets/fa71dfe4-1d74-4bd1-b3c2-ea4e357f1927)

Check the response on the browser.

![app details endpoint on browser using maven with command line](https://github.com/user-attachments/assets/b36b5778-383f-4398-a3fe-9e9c90d0612f)

> [!IMPORTANT]
> Arguments passed via command line have higher precedence and override values coming from any other sources like
`application.properties` file (both internal and external) and environment variables.

:question:**Any Questions**:question:

<br/>

---

---

---

<br/>

## Commit-06 :sparkles:

| **Agenda for this commit**                  |      Covered?      |
|---------------------------------------------|:------------------:|
| 1. Changing the default configuration file. | :white_check_mark: |

### Changing the default configuration file

For whatever reasons, if you do not like `application.properties` as the configuration file name, you can switch to
another file name by specifying the `spring.config.name` environment property.

For example, to look for `app.properties` instead of `application.properties` file, you can run the application as
below:

```bash
java -Dspring.config.name=app -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

or

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar --spring.config.name=app
```

> [!IMPORTANT]
> You need to have `app.properties` file in the classpath (resources folder) in order for the above command to work.

> [!TIP]
> As a matter of convention `-D` is mostly for system and/or environment properties and `spring.config.name` is specific
> to spring, it is better to use the `--` approach.

> [!IMPORTANT]
> `spring.config.name` is used very early in the bootstrapping process to determine which files have to be loaded. So it
> must be defined as an environment property (typically an OS environment variable, a system property, or a command-line
> argument).

To demonstrate this scenario, We have renamed the `application.properties` file to `app.properties`. In the
`HelloController` class, we have used the `@Value` annotation to inject the values from the `application.properties`
file. Since we do not have the `application.properties` file and did not mention any default value in the `@Value`
annotation, it will create the below two problems.

1. When we generate the jar using `./mvnw clean package` the tests are executed automatically and as part of the test,
   Spring tries to create an application context with the `HelloController` bean in it. Since the `HelloController` bean
   should be injected with properties that are NOT available now (because `application.properties` file does not
   exists), the tests fail and the build will fail.
   **[Solution: Set those properties as environment variables before generating the jar or skip the tests]**
2. When we run the application, Spring tries to create an application context with the `HelloController` bean in it.
   Since the `HelloController` bean should be injected with properties that are NOT available now (because
   `application.properties` file does not exists), the application will fail to start.
   **[Solution: Set those properties as environment variables before running the application OR pass them as command
   line arguments while running the application OR specify the `spring.config.name` property so that a file with
   that name (app.properties in our case) will be used as a property source]**

> [!CAUTION]
> If you DID NOT SET those properties as environment variables before running the application or DOES NOT PASS them as
> command line arguments while running the application or DOES NOT SPECIFY the `spring.config.name` property while
> running the application, the **application will NOT start**.

Go to the project root directory.

```bash
cd spring-boot-externalized-configuration-101/
```

![change directory](https://github.com/user-attachments/assets/b989181b-2dbe-4860-bde1-b57501fd681a)

Before renaming the `application.properties` file.

![application properties file before rename](https://github.com/user-attachments/assets/ad31cb6e-41ae-4bd7-aea8-8eb6357fc1fc)

After renaming the `application.properties` file to `app.properties`.

![app properties file after rename](https://github.com/user-attachments/assets/90f25cfc-28fa-49dc-9773-63440381e474)

Generating the jar without skipping the tests will fail.

```bash
./mvnw clean package
```

![maven clean package](https://github.com/user-attachments/assets/7cc05b60-1fcc-4e34-a1a0-c1b8d97f2020)

Since `application.properties` file does not exists, the build will fail. Check the below screenshots.

![app test fail-1](https://github.com/user-attachments/assets/ca9a4191-ab2f-4f46-836e-b479d435f4e1)

![app test fail-3](https://github.com/user-attachments/assets/55de40fe-cd11-490a-8dd6-780d725ce9d0)

Generate the jar by skipping the tests.

```bash
./mvnw clean package -DskipTests=true
```

![maven clean package skip tests](https://github.com/user-attachments/assets/948f7b11-096c-4745-8b83-a0cc8ed276c0)

Running the application without `spring.config.name` will fail. Check the below screenshot.

![app run fail](https://github.com/user-attachments/assets/ed616248-6ae6-4fe4-ab73-4d575823d5a6)

Run the application from command line using java by passing `spring.config.name`.

```bash
java -Dspring.config.name=app -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar
```

![run using java with spring config name as system property](https://github.com/user-attachments/assets/663fd219-08e1-4105-94ac-2f7752fc290e)

or

```bash
java -jar target/spring-boot-externalized-configuration-101-0.0.1-SNAPSHOT.jar --spring.config.name=app
```

![run using java with spring config name as command line arg](https://github.com/user-attachments/assets/e5f49d6e-192d-4593-a948-f9afa6294c68)

Run the application from command line using maven by passing `spring.config.name`.

```bash
./mvnw spring-boot:run -Dspring-boot.run.arguments="--spring.config.name=app"
```

![run using maven with spring config name](https://github.com/user-attachments/assets/cd4ce738-af3a-406b-9d2d-863d5f9786e7)

Check the response on the browser.

![app details endpoint on browser](https://github.com/user-attachments/assets/b45b8635-4f3c-4432-aa43-0a9cb53da8b1)

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
<br><br>
![app details endpoint](https://github.com/user-attachments/assets/7dcea6df-ae02-498b-beb7-36289376a2d1)
<br><br>
![app details endpoint using environment abstraction](https://github.com/user-attachments/assets/7ab09997-0615-4453-8f9a-f232c96ec6dd)
<br><br>
![spring configuration generated from java property sources](https://github.com/user-attachments/assets/64e922ce-2e48-4c7e-990c-2f7ab7cf8f14)
<br><br>
![change directory](https://github.com/user-attachments/assets/a69d50fe-2091-470d-84da-9faf4338b575)
<br><br>
![maven clean package](https://github.com/user-attachments/assets/93540c9a-865b-4a90-bcd1-79c1baaad901)
<br><br>
![target folder and jar generated](https://github.com/user-attachments/assets/e9853a60-559b-4d85-ac3d-416c2b7ac229)
<br><br>
![run using java](https://github.com/user-attachments/assets/44267789-2b19-4ba5-9b95-ccfbe3f32d20)
<br><br>
![app started from console](https://github.com/user-attachments/assets/3d87116d-f1d4-4365-bc1b-6950b72819bd)
<br><br>
![maven run](https://github.com/user-attachments/assets/3d21ec95-4866-41ca-be18-fb810d0a49d3)
<br><br>
![app started from console](https://github.com/user-attachments/assets/b0c94d3a-ad09-4ed5-b085-72650313ef9c)
<br><br>
![app details endpoint](https://github.com/user-attachments/assets/ac5ce80d-c417-4b37-8509-3872c0875501)
<br><br>
![added application properties file externally](https://github.com/user-attachments/assets/512522da-782f-497c-a6d0-756abcaadbff)
<br><br>
![run using java](https://github.com/user-attachments/assets/d1908e31-8d19-4767-b995-495b1d2575fb)
<br><br>
![app started from console](https://github.com/user-attachments/assets/1021e0dc-a10e-46d3-ace6-38a2238175ce)
<br><br>
![app details endpoint](https://github.com/user-attachments/assets/9861c308-1bcb-4c8c-8e16-336496a2f4b1)
<br><br>
![run using maven](https://github.com/user-attachments/assets/44e97ba2-5a9f-40cc-940b-7ea14f65148e)
<br><br>
![app started from console](https://github.com/user-attachments/assets/58edb65b-e6c7-411a-82b2-8b77635767d1)
<br><br>
![application properties file inside target folder](https://github.com/user-attachments/assets/80066648-676d-4b56-b341-7464eba48c99)
<br><br>
![print env](https://github.com/user-attachments/assets/ca60cc4c-3553-410d-b871-aab357fd4139)
<br><br>
![multiple line export](https://github.com/user-attachments/assets/0cde4ea6-668f-41ac-b4ac-9876d7dcceec)
<br><br>
![single line export](https://github.com/user-attachments/assets/10f22d9c-38fa-4117-b3be-4669b8fb38dc)
<br><br>
![multiple line echo](https://github.com/user-attachments/assets/a30b19a9-5b0a-4fd5-8761-31c9eababde3)
<br><br>
![single line echo](https://github.com/user-attachments/assets/f7529f44-a2bd-435c-b35a-ebf97a6df91e)
<br><br>
![change directory](https://github.com/user-attachments/assets/b989181b-2dbe-4860-bde1-b57501fd681a)
<br><br>
![maven clean package](https://github.com/user-attachments/assets/7cc05b60-1fcc-4e34-a1a0-c1b8d97f2020)
<br><br>
![run using java](https://github.com/user-attachments/assets/59136705-6009-488b-8575-a6429a1d391d)
<br><br>
![app details endpoint on browser](https://github.com/user-attachments/assets/22ac35c2-b33a-418d-8042-d396fd19cc4e)
<br><br>
![run using maven](https://github.com/user-attachments/assets/d84345b3-b9c1-48f9-8347-3183db1a90c9)
<br><br>
![multiple line echo before setting env variables](https://github.com/user-attachments/assets/ffde260a-4fed-4199-bac9-42f3726e1e5c)
<br><br>
![single line echo before setting env variables](https://github.com/user-attachments/assets/9d53d38b-310f-4443-94ac-42178c8ce3d7)
<br><br>
![app details endpoint on browser before setting env variables](https://github.com/user-attachments/assets/d2d947ff-514c-443b-96a2-7c07a155ac3a)
<br><br>
<br><br>
<br><br>
<br><br>
![run using java with command line-1](https://github.com/user-attachments/assets/997500be-5edc-43d1-9e42-e8875bc7c495)
<br><br>
![run using java with command line-2](https://github.com/user-attachments/assets/e8d01ea6-094e-413d-a1dc-cc2a3975e1cc)
<br><br>
![run using maven with command line](https://github.com/user-attachments/assets/fa71dfe4-1d74-4bd1-b3c2-ea4e357f1927)
<br><br>
![app details endpoint on browser using java with command line-1](https://github.com/user-attachments/assets/d5300fca-714d-40c9-b0af-ec85c8882871)
<br><br>
![app details endpoint on browser using java with command line-2](https://github.com/user-attachments/assets/68f8717c-aba4-4314-acd9-3c2ad040b31e)
<br><br>
![app details endpoint on browser using maven with command line](https://github.com/user-attachments/assets/b36b5778-383f-4398-a3fe-9e9c90d0612f)
<br><br>
<br><br>
<br><br>
<br><br>
![maven clean package skip tests](https://github.com/user-attachments/assets/948f7b11-096c-4745-8b83-a0cc8ed276c0)
<br><br>
![run using java with spring config name as system property](https://github.com/user-attachments/assets/663fd219-08e1-4105-94ac-2f7752fc290e)
<br><br>
![run using java with spring config name as command line arg](https://github.com/user-attachments/assets/e5f49d6e-192d-4593-a948-f9afa6294c68)
<br><br>
![app test fail-1](https://github.com/user-attachments/assets/ca9a4191-ab2f-4f46-836e-b479d435f4e1)
<br><br>
![app test fail-3](https://github.com/user-attachments/assets/55de40fe-cd11-490a-8dd6-780d725ce9d0)
<br><br>
![app run fail](https://github.com/user-attachments/assets/ed616248-6ae6-4fe4-ab73-4d575823d5a6)
<br><br>
![application properties file before rename](https://github.com/user-attachments/assets/ad31cb6e-41ae-4bd7-aea8-8eb6357fc1fc)
<br><br>
![app properties file after rename](https://github.com/user-attachments/assets/90f25cfc-28fa-49dc-9773-63440381e474)
<br><br>
![run using maven with spring config name](https://github.com/user-attachments/assets/cd4ce738-af3a-406b-9d2d-863d5f9786e7)
<br><br>
![app details endpoint on browser](https://github.com/user-attachments/assets/b45b8635-4f3c-4432-aa43-0a9cb53da8b1)
<br><br>
<br><br>
<br><br>
<br><br>
<br><br>
