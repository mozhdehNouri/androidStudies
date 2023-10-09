1- benefit of kotlin over java  :

- It's Completely Interoperable with Java. ...
- It's (way) More Concise than Java. ...
- Its Code's Safer. ...
- It Comes with a Smarter and Safer Compiler. ...
- It's Easier to Maintain. ...
- It's Been Created to Boost Your Productivity. ...
- It “Spoils” You with Better Support for Functional Programming.
- https://www.cmsdrupal.com/blog/what-are-biggest-advantages-kotlin-over-java-8-reasons-try-it-android-developmenthttps://www.cmsdrupal.com/blog/what-are-biggest-advantages-kotlin-over-java-8-reasons-try-it-android-development

**

##### 1. Readability

## 1. Readability

## 2. Null-Safe

## 3. Using Getters and Setters

## 4. Interoperability

## 5. Immutability

## Functional Programming

Another significant difference between Java and Kotlin is their support for functional programming. While Java has added some support for functional programming in recent years with the release of **Java 8**, Kotlin was designed from the ground up to support functional programming concepts.

[Kotlin VS Java – What&#39;s the Difference?](https://www.freecodecamp.org/news/kotlin-vs-java-whats-the-difference/)

[Which is better, Kotlin vs. java? Let&#039;s settle the debate](https://dac.digital/kotlin-vs-java/)

[Kotlin vs Java: strengths, weaknesses and when to use which](https://kruschecompany.com/kotlin-vs-java/)

[What Are The Advantages of Kotlin Over Java?](https://alignminds.com/advantages-kotlin-over-java/)

----

2 - expalin use of lateinit and lazy keyword 

 lateinit allows us to postpone the initialization of a property until a later time, while lazy initializes the property only when it is accessed for the first time.**

**

# What is the Lateinit?

We may **not want to initialize our values at declaration time**, but instead **want to initialize and use them at any later time in our application.** However, **before using our value,** we must not forget that **our value must be initialized before it can be used.** Let’s make an example for better understanding!

# What is the by Lazy?

Lazy initialization **is a design pattern that we often come across in the software world**. With lazy initialization, we can **create objects only the first time that we access them**, **otherwise we don’t have to initialize them.** It ensures that objects that are expensive to create are **initialized only where they are to be used, not on the app startup.**

# What are the differences between Lateinit and Lazy?

1**-** The modifier **“lateinit” is restricted to mutable(var) variable properties,** whereas the modifier “**lazy” is exclusively used with read-only(val) properties.**

2- A property marked with “lateinit” **can be assigned a value multiple times as needed during runtime**, whereas a property initialized with “lazy” can **only be assigned a value once upon its first use.**

3- It is **not possible to declare a primitive data type as a “lateinit” property,** whereas a **“lazy” property can be of either primitive or non-primitive data types.** (Such as Int)

4- While it is **not possible to ensure thread safety for a “lateinit” property**, for a **“lazy” property we have the option to choose from synchronization options such as SYNCHRONIZED, PUBLICATION, or NONE. (**That’s why we use lazy when using the Singleton design pattern.**)**

5- Unlike a **“lateinit” property, which cannot be declared as nullable**, a “lazy” property can be defined **as either nullable or non-nullable.**

6- While a **“lateinit” property cannot have a customized getter**, a **“lazy” property contains a block of code that runs the first time the property is called.**

7- Attempting to access a “lateinit” property **before it has been initialized results in a distinct exception that specifies the uninitialized property.** On the other hand, a **“lazy” property cannot be accessed before its initialization.** It is important to **note that a “lazy” property can be null, yet it will still be initialized the first time the property is accessed.**

https://medium.com/huawei-developers/kotlin-lateinit-vs-by-lazy-initialization-example-tutorial-c19d84216480

https://medium.com/huawei-developers/kotlin-lateinit-vs-by-lazy-initialization-example-tutorial-c19d84216480

--------

3- how to check lateinit class iinitialized or not ?

Using `.isInitialized` property one can check initialization state of a lateinit variable.

```kotlin
lateinit var file: File    

if (this::file.isInitialized) { ... }
```

----

4- diffrent between publick and open 
The *open* keyword allows classes, functions, and properties to be extended, while *public* is a visibility modifier that doesn’t have any explicit usage since all classes, functions, and properties are publicly visible by default.

The `open` keyword means “open for extension“ - i.e. it's possible to create *subclasses* of an `open` class:

> The `open` annotation on a **class** is the opposite of Java's `final`: it allows others to inherit from this class. By *default*, all classes in Kotlin are `final`, which corresponds to **Effective Java, Item 17: Design and document for inheritance or else prohibit it**.

You also need to be explicit about methods you want to make overridable, also marked with `open`:

```kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}
```

The `public` keyword acts as a [visibility modifier](https://kotlinlang.org/docs/visibility-modifiers.html) that can be applied on classes, functions, member functions, etc. If a top-level class or function is `public`, it means it can be used from other files, including from other modules. Note that `public` is the default if nothing else is specified explicitly:

> If you do not specify any visibility modifier, `public` is used by default, which means that your declarations will be visible everywhere
