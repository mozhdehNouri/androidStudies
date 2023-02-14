 companion object Vs Object:

first of all you have to konw about static in java :

```kotlin
public class Mentor {
    public void guide() {
    }
}
```

Here, if we have to call the method `guide()` , we will have to create an object of the `Mentor` class and then call.

```kotlin
Mentor mentor = new Mentor();
mentor.guide();
```

So, how can we call the method without creating the object of the class?

The answer is the `static` keyword in Java.

```kotlin
public class Mentor {
    public static void guide() {
    }
}
```

how we can call a method without creating a boject in kotlin?
Answer: **companion object**.

```kotlin
class Mentor {
    companion object {
        fun guide() {
        }
    }
}
```

note : you  can define variable too.

```kt
class Mentor {
    companion object {
        const val MAX_SLOTS = 10
        fun guide() {
        }
    }
}
```

you can access two way :
one :

```kt
val maxSlots = Mentor.MAX_SLOTS
```

second : 

```kt
val maxSlots = Mentor.Companion.MAX_SLOTS
```

note :

We can also name our companion object as we have done below:

```kt
class Mentor {
    companion object Config {
       const val MAX_SLOTS = 10
    }
}
```

```kt
val maxSlots = Mentor.Config.MAX_SLOTS
```

or 

```kt
val maxSlots = Mentor.MAX_SLOTS
```

But the companion reference name is redundant.

important point :

- It cannot be defined outside a class.
- The object is common in all instances of the class.
- It is instantiated for the first time as soon as the containing class is loaded. It means that it is instantiated even if we have not used the companion object.



note : actully `companion object` equals `const `keyword in java.

#### what is Object :

```kt
object Config {
    const val MAX_SLOTS = 10
}
```

access :

```kt
val maxSlots = Config.MAX_SLOTS
```

Defining inside a class:

```kotlin
class Mentor {

    object Config {

        const val MAX_SLOTS = 10

    }

}
```

access :

```kt
val maxSlots = Mentor.Config.MAX_SLOTS
```

diff between regular Object and companion object :

| Companion object                                                                                                                                                                   | Regular object                                                                                    |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| It needs to be defined inside a class.                                                                                                                                             | It can be defined anywhere.                                                                       |
| The companion object is instantiated for the first time as soon as the containing class is loaded. It means that it is instantiated even if we have not used the companion object. | The object is instantiated lazily when we access it for the first time.                           |
| It is equivalent to a static keyword in Java.                                                                                                                                      | Mainly used for providing Singleton behavior.                                                     |
| Gets a default name as a Companion when we do not provide a name.                                                                                                                  | Must be named by us.                                                                              |
| We can skip the name while calling a method or accessing a variable.                                                                                                               | If defined inside a class, we can't skip the name while calling a method or accessing a variable. |

Resource :

[Companion object in Kotlin](https://amitshekhar.me/blog/companion-object-in-kotlin)
