###### JvmOverloads Annotation in Kotlin :

kotlin have interoperability with java And vice versa.

it means that the existing java code can be called from kotlin and also the kotlin code can be called from java.



Consider the example :

```kt
data class Session(val name:String , val currentTime:Date = Date())
```

  we can create  an object in kotlin like below :

```kt
val one = Session("one",Calendar.getInstance().getTime())

val two = Session("two")
```

create an object in java :

```kt
Session one =Session("Session",Calendar.getInstance().getTime())

Session two =Session("two")  //compilation error
```



how could solve this problem ?

answer : @JvmOverloads

java dose not support defualt parameter.it support overloading

and kotlin support defaul parameters.

so we will have to use @JvmOverloads.



Update your data class as below :

```kt
data class Session @JvmOverloads constructor(val name:String , val time :Date())
```

kotlin compiler to generate overloads for the constructor.

and finally you can use in java like below :

```kt

Session one =Session("Session",Calendar.getInstance().getTime())

Session two =Session("two")

```


