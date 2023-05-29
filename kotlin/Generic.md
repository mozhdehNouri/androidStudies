### Generic

**Generics** are the powerful features that allow us to define classes, methods and properties which are accessible using different data types while keeping a check on the **compile-time** type safety.

We always use angle brackets **<>** to specify the type parameter in the program.

Generic programming is a way of writing our code in a flexible manner like we would in a dynamically-typed language. At the same time, generics allow us to write code safely and with as few compile-time errors as possible.

Using generics in Kotlin enables the developer to focus on creating reusable solutions, or templates, for a wider range of problems.

consider this example  :

we have a back stack class like this :

```kt
class IntStack {

    private val elements: MutableList<Int> = ArrayList() 

    fun pop(): Int {
        return elements.removeLast()
    }

    fun push(value: Int) {
        elements.add(value)
    }
    // ...
}
```

nothing fancy for now. but what happens if we need to store integer strings,or even puppies? Then we’d need to create two more classes: `StringStack` and `PuppyStack`.

Would the puppy stack do anything differently than the integer stack? Of course not. As a result, there’s no need to create separate classes for each case. It’s enough to create a generic stack that can be used anywhere in our project :

```kt
class Stack<T> {

    private val elements: MutableList<T> = ArrayList()

    fun pop(): T {
        return elements.removeLast()
    }

    fun push(value: T) {
        elements.add(value)
    }
    // ...
}
```

Now we can use this data structure for stacking anything we want.

##### Considewr another example:

this code find spesfic element 

```kt
class ArrayUtil(private val array :Array<Int>){

fun findElement(element:Int,foundElement(index:Int,element:Int?)-> Unit){

// i is indices and array[i] are value?
for(i in array.indices)    {
if(array[i]==element){
    foundElement(i,array[i])
    return
}   

}
   foundElement(-1,null)
    return
}    

}


fun main(){

val arrayUtil = ArrayUtil(arrayOf(1,2,3,4,5))
arrayUtil.findElement(3){index,element ->
    println("$index")
    println("$element")
}
}
```

above example is only work for Int type if we want search on String type we should create another class.

now we can use generic 

look at this example :

```kt
class ArrayUtil<T>(private val array :Array<T>){

fun findElement(element:T,foundElement(index:Int,element:T?)-> Unit){

// i is indices and array[i] are value?
for(i in array.indices)    {
if(array[i]==element){
    foundElement(i,array[i])
    return
}   

}
   foundElement(-1,null)
    return
}    

}


fun main(){

val arrayUtil = ArrayUtil<Int>(arrayOf(1,2,3,4,5))
val arrayUtil1 = ArrayUtil<String>(arrayOf(1,2,3,4,5))
arrayUtil.findElement(3){index,element ->
    println("$index")
    println("$element")
}
}
```

We can do several input like X,T

```kt
class ArrayUtil<T,X,C>(private val array :Array<T>){}



// usage 

fun main (){
val a = ArrayUtil<String,Int,Float>  

}
```

define a generic class :

```kt
class MyClass<T>(text: T) {
    var name = text
}
```

and to create an instance of the class we need to act like this :

```kt
val my : MyClass<String> = Myclass<String>("Mozhdeh")
```

look at this example :

```kt
val my = MyClass("Mozhdeh") 
```

Mozhdeh  has type String, so the compiler figures out that we are talking about Myclass<String>

##### **Advantages of generic:**

1. **Type casting is evitable-** No need to typecast the object.
2. **Type safety-** Generic allows only a single type of object at a time.
3. **Compile time safety-** Generics code is checked at compile time for the parameterized type so that it avoids run-time error.

look at the example :

```kt
class Company (text: String) {
    var x = text
    init{
        println(x)
    }
}
fun main(args: Array<String>){
    var name: Company = Company("GeeksforGeeks")
    var rank: Company = Company(12)// compile time error
}
```

output :

```kt
Error:(10, 33) Kotlin: The integer literal does not conform to the expected type String
```

In order to solve the above problem, we can create a generic type class that is user defined accepts the different types of parameters in a single class. The class Company of type is a general type class that accepts both Int and String types of parameters.

```kt
class Company<T> (text : T){
    var x = text
    init{
        println(x)
    }
}
fun main(args: Array<String>){
    var name: Company<String> = Company<String>("GeeksforGeeks")
    var rank: Company<Int> = Company<Int>(12)
}
```

###### 

###### look at this example :

```kt
fun main(args: Array<String>){  
val stringList: ArrayList<String> = arrayListOf<String>("Ashu","Ajay")  
val s: String = stringList[0]  
println("printing the string value of stringList: $s")  
printValue(stringList)  
val floatList: ArrayList<Float> = arrayListOf<Float>(10.5f,5.0f,25.5f)  
printValue(floatList)     
}  
fun <T>printValue(list: ArrayList<T>){  
    for(element in list){  
println(element)  
    }  
}  
```

output :

```kt
printing the string value of stringList: Ashu
Ashu
Ajay
10.5
5.0
25.5
```

In above example, we are accessing the generic method of collection type (ArrayList). For doing this, we create two different objectsof ArrayList class arrayListOf<String>("Ashu","Ajay") and arrayListOf<Float>(10.5f,5.0f,25.5f) of String and Float types respectively. When we call the generic method <T>printValue(list: ArrayList<T>) using printValue(stringList), the type **T** of method <T>printValue(list: ArrayList<T>)will be replaced by String type. Similarly, when we call the generic method using printValue(floatList), the type **T** of method <T>printValue(list: ArrayList<T>) will replace by Float type.

### Kotlin generic extension function

As extension function allows to add methods to class without inherit a class or any design pattern.

In this example, we add a method printValue()to ArrayList class of generic type. This method is called form stringList.printValue() and floatList.printValue()of String and Float types respectively. As **"this"** keyword in extension function represent the current calling instance. When we call the extension function using stringList.printValue(), the **this** represents stringList instance containing String type values. Similarly, calling the extension function using floatList.printValue(), the **this** represents floatList instance containing Float type values.

```kt
fun main(args: Array<String>){  
val stringList: ArrayList<String> = arrayListOf<String>("Ashu","Ajay")  
stringList.printValue()  
val floatList: ArrayList<Float> = arrayListOf<Float>(10.5f,5.0f,25.5f)  
floatList.printValue()  
}  
fun <T>ArrayList<T>.printValue(){  
    for(element in this){  
println(element)  
    }  
}  
```

output :

```kt
Ashu
Ajay
10.5
5.0
25.5
```

By default, Kotlin makes arrays "invariant," which means that an array of one type cannot be assigned to an array of a different type. Similarly, generic types in Kotlin are also invariant by default.

To manage this behavior, Kotlin provides the keywords "in" and "out."  which allows you to specify whether a generic type can accept or return a subtype or supertype

Variance is of *two* types-

1. **Declaration-site variance(using in and out)**
2. **Use-site variance: Type projection**

By using "out," you can declare that a generic type can only produce (return) values of a specific type or its subtypes.

Conversely, by using "in," you can declare that a generic type can only consume (accept) values of a specific type or its supertypes.





# Deep dive into generic :

One of the trickiest aspects of Java's type system is the wildcard types. Kotlin doesn't have these. Instead, Kotlin has declaration-site variance and type projections.



# Wildcards in Java :

The **question mark (?)** is known as the **wildcard** in generic programming. It represents an unknown type. The wildcard can be used in a variety of situations such as the type of a parameter, field, or local variable; sometimes as a return type. Unlike arrays, different instantiations of a generic type are not compatible with each other, not even explicitly. This incompatibility may be softened by the wildcard if ? is used as an actual type parameter.



### Kotlin Generics - What is Variance?

































Resource :

https://www.geeksforgeeks.org/kotlin-generics/

https://www.javatpoint.com/kotlin-generics

https://blog.logrocket.com/understanding-kotlin-generics/

[Generics, objects, and extensions &nbsp;|&nbsp; Android Developers](https://developer.android.com/codelabs/basic-android-kotlin-compose-generics#1)

[Generics: in, out, where | Kotlin Documentation](https://kotlinlang.org/docs/generics.html#variance)

[Kotlin Generics and Variance - Kotlin Expertise Blog](https://kotlinexpertise.com/kotlin-generics-and-variance-vs-java/)

[Wildcards in Java - GeeksforGeeks](https://www.geeksforgeeks.org/wildcards-in-java/)
