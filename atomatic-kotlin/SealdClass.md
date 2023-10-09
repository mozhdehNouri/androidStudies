## Sealed Class

A sealed class defines a set of subclasses within it.

To constrain a class hierarchy, declare the superclass sealed."

- This sentence means that if you want to restrict or control the hierarchy of classes (i.e., how classes are organized and inherited from each other), you should declare the superclass as sealed. In Kotlin, a sealed class is used as a superclass to define a limited set of subclasses that are allowed to inherit from it.

sealed classes are a feature unique to Kotlin and are not available in Java.

you can create sealed classes, which have certain special characteristics compared to regular classes. Sealed classes are used to define class hierarchies that are limited or restricted in some way. You specify all possible subclasses of a sealed class, and no other subclasses can be created outside of this hierarchy.

When you create a sealed class, you explicitly list its possible subclasses. These are the only classes that can inherit from the sealed class.

Sealed classes are useful when you know that a value or object will belong to one of the defined subclasses, and you want to ensure that no other types can be introduced.
