##### what is init block exactly in kolin ?

first of  all lets pay to consept of priamary and secondry constractor 

- A class in Kotlin can have a *primary constructor* and one or more *secondary - constructors*.

- The primary constructor is a part of the class header, and it goes after the class name and optional type parameters.

- A class can also declare *secondary constructors*, which are prefixed with `constructor`

###### in this  example we have Student class with primary constractor

```kotlin
class Student(firstName:String, secondName:String){

}
```

##### in this example :

```kotlin
class Student(firstName:String, secondName:String){

// secondry constracutor 
constructor(firstName:String, secondName:String , age :Int):this(firstName, secondName)
//inside secondry constructor     
// code to do something 

}
```

the first major diffrent  : primary costracture can not contain any code but secondry can 

solution :

Initialization code can be placed in *initializer blocks* prefixed with the `init` keyword.

you can use init block {}

actully init blocking  and  secondry constracture can contain any code 

lets take a example :

```kotlin
```kotlin
class Student(firstName:String, secondName:String){

init {
    val fullName = firstName + secondName
    println(fullName)
}
}
```

```
question : when dose init block get called in kotlin ?

answer : the init block gets called immediately after the primary constructor but before the secondary constructor

things to konw while using the init block in kotlin :

- the init block gets executed immediately after the primary constructre.

- the init block gets executed before the secondry constructre.

- primary constructor parameters can be used in the initializer blocks.

- a class can have more than one init block they are executed sequentially , in this case , the initializer blocks are executed in the same order as they appear in the class body considering the properties if there are any in between.
- init blocking .
- it dose not take any parameters.
```

look at this example :

```kotlin
```kotlin
class Student(firstName:String, secondName:String){


init {
    println("first init block $firstname")
}

private val fullName  = "$firstName $secondName".also {
    println("its full name property")
}

init {
    println("second init Block :${fullName.lenght}")
}

constructor(firstName:String, secondName:String , age :Int):this(firstName, secondName)
println("secondary constractor $age")

}
```

```
and next when we create an abject of Studen class using secondary constractor as below :


```kotlin
Student("mozhdeh","nouri",24)
```

Output :

```
first init block :mozhdeh
its full name :property
second init Block :12
secondary constractor :24
```

another example :

```kotlin
class InitOrderDemo(name: String) {
    val firstProperty = "First property: $name".also(::println)

    init {
        println("First initializer block that prints $name")
    }

    val secondProperty = "Second property: ${name.length}".also(::println)

    init {
        println("Second initializer block that prints ${name.length}")
    }
}
```

output:

```
First property: hello
First initializer block that prints hello
Second property: 5
Second initializer block that prints 5
```

when we use the init block in kotlin ?

answer : when we have a perform a task during the initialization of an abject and we do not have a neccessity for a secondary constructor. Using primary constructor  with the init block.

question: why property print before second init block ?

in according google doc :

During the initialization of an instance, the initializer blocks are executed in the same order as they appear in the class body, interleaved with the property initializers

so the code in all initializer blocks and property initializers is executed before the body of the secondary constructor

the nest example is using  init block in viewModel

loock at this example:

```kotlin
Class StudenViewModel(private val repo:StudentRepository):ViewModel(){
init {
    fetchStudent()
}    

fun fetchStudent(){

// code     

}

}
```

Resource :

https://www.youtube.com/watch?v=cb3jOFozJns

[Classes | Kotlin Documentation](https://kotlinlang.org/docs/classes.html)
