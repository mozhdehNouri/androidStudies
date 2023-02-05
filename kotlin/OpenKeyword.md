Open Keyword :



Well in Kotlin by default all the classes are final which means they are not- inheritable, so for Inheritance, we need to use a keyword **open** in your class

```kotlin
class Student(){
    
}
```

```kotlin
class School():Student(){
    

// get error becuse Student class not open
}
```

finally :

```kotlin
open class Student(){
    
}
```




