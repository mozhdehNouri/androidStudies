##### lateinit vs lazy in Kotlin :

## lateinit :

`lateinit` in Kotlin is useful in a scenario when we do not want to initialize a variable at the time of the declaration and want to initialize it at some later point in time.

```kt
private lateinit var mentor: Mentor
```

mentor variable is `non-nullable`. beacuse of useing lateinit your variable can not be nullabe.

And later we can initialize the variable when we need it as below:

```kt
fun bookASlot() {
    mentor = Mentor()
}
```

In Kotlin, we also have a way to check if the `lateinit` variable is initialized or not by using as below :

```kt
if(this::mentor.isInitialized) {
    // access mentor
} else {
    // do something else
}
```

if we want to create a nullabe varibale we can create variable as below :

```kt
private var mentor: Mentor? = null
```



lateinit point :

- Can be only used with the `var` keyword.
- Can be only used with a `non-nullable` variable.
- Should be used if the variable is mutable and can be initialized later.
- Should be used if you are sure about the initialization before use.



lazy :

`lazy` in Kotlin is useful in a scenario when we want to create an object inside a class, but that object creation is expensive and that might lead to a delay in the creation of the object that is dependent on that expensive object.

```kt
class Session {
    private val mentor: Mentor = Mentor()
}
```

Suppose `Mentor` is an expensive object. And `Session` is the object that is dependent on the `Mentor` object.

If `Mentor` object creation takes time, it will delay the creation of `Session` object.

So, this is where the `lazy` keyword in Kotlin will help us.

```kt
class Session {
    private val mentor: Mentor by lazy { Mentor() }
}
```

object `mentor` will get initialized only when it is accessed for the first time, else it will not get initialized.



lazy point :

- Can be only used with the `val` keyword, hence `read-only` property.
- We want the variable to be initialized only if we need it for the first time.
- Must understand that it only creates the object when we access it for the very first time and then in the subsequent access, it returns the same object.