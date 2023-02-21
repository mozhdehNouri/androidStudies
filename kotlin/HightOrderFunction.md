###### Higher-Order Functions and Lambdas in Kotlin:

lambda : pass them as arguments to functions, return them, or do any other thing we could do with a normal object

lambda example :

```kt
val square : (Int) -> Int = { value -> value * value }

//call
val nine = square(3)
```

example :

```kt
val doNothing : (Int) -> Int = { value -> value }
```

- **`{ value -> value }`** is a complete function in itself. It takes an **int** as a parameter and returns a value as an **`int`**.

- In **`(Int) -> Int`**

- **`(Int)`** represents input **`int`** as a parameter.

- **`Int`** represents return type as an **`int`**.

- So, the **`doNothing`** is a function in itself that takes a value as an **`int`** and returns the same value as an **`int`**.

##### Higher-Order Functions :

A higher-order function is a function that takes functions as parameters or returns a function.

Instead of Integer, String or Array as a parameter to function, we will pass anonymous function or lambdas.

it's a function which can take do two things:

- Can take functions as parameters
- Can return a function
- 

note :

We can pass a lambda expression as a parameter to Higher-Order Function.   
There are **two** types of lambda expression which can be passed- 

- Lambda expression which return Unit
- Lambda expression which return any of the value integer,string etc

```kt
fun testHigherOrderFunction(doSomethong :()->Unit ){

/// do some code 

doSomethong()
}

//call
testHigherOrderFunction(
        {
          val user = User()
          user.name = "ABC"
          println("Lambda is awesome")
        }
)
```

explation:

**`()`** represents that the function takes no parameters.

**`Unit`** represents that the function does not return anything.

##### We can return a function :

```kt
fun add(a: Int, b: Int): Int {
  return a + b

}


fun returnMeAddFunction(): ((Int, Int) -> Int) {
   // can do something and return function as well
   // returning function
   return ::add
}

//call
fun main(){
   val add = returnMeAddFunction()       // val add: (Int, Int) -> Int

   val result = add(2, 2)               // val result: Int 

}
```

- **`(Int, Int)`** means that the function should take two parameters both as the **`int`**.

- **`Int`** means that the function should return value as an **`int`**.

example :

```kt
// lambda expression
var lambda = {
    println("GeeksforGeeks: A Computer Science portal for Geeks") }
// higher-order function
fun higherfunc( lmbd: () -> Unit ) {     // accepting lambda as parameter
    lmbd()                               //invokes lambda expression
}
fun main(args: Array<String>) {
    //invoke higher-order function
    higherfunc(lambda)                 // passing lambda as parameter
}
```

output :

```kt
GeeksforGeeks: A Computer Science portal for Geeks
```

example 2 :

```kt
    // lambda expression
var lambda = {a: Int , b: Int -> a + b }
    // higher order function
fun higherfunc( lmbd: (Int, Int) -> Int) {      // accepting lambda as parameter

    var result = lmbd(2,4)    // invokes the lambda expression by passing parameters                   
    println("The sum of two numbers is: $result")
}

fun main(args: Array<String>) {
    higherfunc(lambda)           //passing lambda as parameter
}
```

output :

```kt
The sum of two numbers is: 6
```

Example 3 :

```kt
    // regular function definition
fun printMe(s:String): Unit{
    println(s)
}
   // higher-order function definition
fun higherfunc( str : String, myfunc: (String) -> Unit){
   // invoke regular function using local name
    myfunc(str)
}
fun main(args: Array<String>) {
    // invoke higher-order function
    higherfunc("GeeksforGeeks: A Computer Science portal for Geeks",::printMe)
}
```

output

```kt
GeeksforGeeks: A Computer Science portal for Geeks
```



example 4 :

Then, we define the higher-order function having return type is a function.

```kt
fun higherfunc5() : ((Int,Int)-> Int){
    return ::mul
}
```

**::mul** represents that it return mul() function







Resource:

[Higher-Order Functions and Lambdas in Kotlin](https://amitshekhar.me/blog/higher-order-functions-and-lambdas-in-kotlin)

[Kotlin Higher-Order Functions - GeeksforGeeks](https://www.geeksforgeeks.org/kotlin-higher-order-functions/)
