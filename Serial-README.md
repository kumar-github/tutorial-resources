# Serialization Deserialization Demo
> **Every day is a learning day.**

This project will walk you through a simple demo of Serialization and Deserialization in Java. This is **NOT** going to be an in-depth tutorial about Serialization and Deserialization. Instead, this will cover the basics and act as refresher for my another tutorial `jackson-demo`. As usual, the concepts (though few) are covered in-depth and organized as individual git commits. The commits are numbered sequentially starting from `00`, `01`, `02` and so on. As you go through it, take the time to understand the changes each step makes to the code. You can clone the entire project to your local machine and then start applying the commits one by one starting from `00`. This `README.md` file will be updated in every commit and will tell you what has been covered in the specific commit.

<br/>

---

---

---

<br/>

Table Of Contents
=================

* [Serialization Deserialization Demo](#serialization-deserialization-demo)
  * [Few things about Serialization Deserialization](#few-things-about-serialization-and-deserialization)
  * [Complete Reference](#complete-reference)
  * [Requirements](#requirements)
  * [Run Locally](#run-locally)
   
## Few things about Serialization and Deserialization

The Java platform allows us to create reusable objects in memory. However all of those objects exist only as long as the **Java Virtual Machine** remains running. It would be nice if the objects we create could exist beyond the lifetime of the **JVM**. With *Serialization* and *Deserialization*, you can flatten your objects, store, transmit and reuse them.

**Serialization** is the process of saving an object's state in to a sequence of bytes.

**Deserialization** (*reverse of Serialization*) is the process of rebuilding those bytes into a live object.

The main reason behind **Serialization** and **Deserialization** is to store and/or transmit the objects and then reconstruct the same object (*not same but a replica*) in different contexts. The contexts could be:
1. Needing the same object in the same JVM but at a later time.
2. Needing the same object in a different JVM, which might be on a different machine.
3. Needing the same object in a non-JVM application.

<br/>

---

---

---

<br/>

## Complete Reference

[Serialization Deserialization Reference](https://docs.oracle.com/javase/8/docs/platform/serialization/spec/serialTOC.html)

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
git clone https://github.com/kumar-github/serialization-deserialization-demo
```

Go to the project root directory

```bash
cd serialization-deserialization-demo
```

Start the service

```bash
./mvnw clean compile exec:java -Dexec.mainClass=tech.badprogrammer.Main
```

<br/>

---

---

---

<br/>

![Serialization](https://github.com/kumar-github/tutorial-resources/assets/2657313/540ccaff-a224-4f67-b976-1130b309c8a5)

![Deserialization](https://github.com/kumar-github/tutorial-resources/assets/2657313/2915c8ab-2450-4d88-9c07-06c63340f1e0)



Secrets of the Java Serialization API

The Java Serialization API provides a standard mechanism for developers to handle object serialization and deserialization.

We'll see three different ways to perform serialization and deserialization
1. Using the default protocol.
2. Customizing the default protocol.
3. Creating our own protocol.

The Default Protocol
To save an object in Java, we must have a savable (or serializable) object. An object can be made savable or serializable by implementing the `Serializable` interface, which signifies to the underlying API that the object can be flattened into bytes and subsequently inflated in the future.

*Note: `Serializable` is a marker or tag interface. It does not have any methods to implement. It simply allows the serialization mechanism to verify that the class can be serialized.*

Rules of Serialization:
Rule #1: The object to be serialized must implement the `Serializable` interface or inherit that implementation from its object hierarchy.
Rule #2: The object to be serialized must mark all non serializable fields as `transient`.

We can use the `ObjectOutputStream` class to serialize an object. It is wrapped around a lower-level byte stream to handle the serialization protocol for us. If needed, we could easily transfer a flattened object across a network wire and have it be rebuilt on the other side.

The `writeObject()` method kicks off the serialization mechanism by flattening the object first and saving to a file.

We can use the `ObjectInputStream` class to deserialize an object.

The `readObject()` method kicks off the deserialization mechanism by inflating the object from the file. The `readObject()` method reads in the raw bytes that we previously persisted and creates a live object that is an exact replica of the original. Because `readObject()` can read any serializable object, a cast to the correct type is required.

Later, we simply call the getTime() method to retrieve the time that the original object flattened. The flatten time is compared to the current time to demonstrate that the mechanism indeed worked as expected.
