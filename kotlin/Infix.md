#### infix notation in kotlin :

the infix notation enable us to write code that looks more like a natural language.

infix notation allows a function to be calles without use of dot and **parentheses.**

In Kotlin, a functions marked with **infix** keyword can also be called using infix notation means calling without using parenthesis and dot.

infix can be be a member function or Extension function . they have a single parameter .they hasbe prefix of infix

what is member function :

In Kotlin, a member function is a function that is defined within a class or an object. Member functions are also sometimes referred to as methods.

A member function can access the properties and other member functions of its containing class or object. 

When calling a member function, it is typically called on an instance of the class or object, and the function operates on the state of that instance.

```kts
class MyClass {
var myProperty: String = ""

fun myFunction() {
println("The value of myProperty is $myProperty")
}
}
```



There are **two** types of infix function notation in Kotlin :

1. Standard library infix function notation
2. User defined infix function notation



Standard library infix function notation:

```kt
fun main(args: Array<String>) {
    var a = 8
 
    // // call using infix notation
    var result1 = a shr 2
    println("Signed shift right by 2 bit: $result1")
    // call using dot and parenthesis
    var result2 = a.shr(1)
    println("Signed shift right by 1 bit: $result2")
}
```

```kt
fun main(args: Array<String>) {
    var a = 8
    var b = 4
    
    println(++a)      // call using infix notation
    println(a.inc())  // call using dot and parenthesis
    println(--b)      // call using infix notation
    println(b.dec())  // call using dot and parenthesis
}
```

output:

```kt
9
10
3
2
```

**Explanation:**   
Here, we have used increment and decrement operators using infix notations.   
++a represents a(8) + 1 so it prints 9   
a.inc() also represents a(9) + 1 so it prints 10   
–b represents b(4) – 1 = 3   
b.dec() also represents b(3)- 1 = 2







lets take an example :

```kt
infix fun Int.add(value: Int): Int = this + value
```

and use as below :

```kt
val sum = 5 add 10
```

for more deep undrstanding :

```kt
val map = mapOf(
    1 to "Amit Shekhar",
    2 to "Anand Gaurav",
    3 to "Lionel Messi"
)
```

Here `to` for the Pair is possible only because of the infix.

source code of Pair :

```kt
infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
```

This is why we are able to create `Pair` using infix notation.

some rule of infix :

- Infix function calls have lower precedence than arithmetic operators. It means that `5 add 10 + 15` is equivalent to `5 add (10 + 15)`

- Infix function calls have higher precedence than the boolean operators `&&` and `||`. It means that `a && b xor c` is equivalent to `a && (b xor c)`

- They must be member functions or extension functions.

- They must have a single parameter.

- The parameter must not accept a variable number of arguments.

- The parameter must not have a default value.

- It must be member function or extension function
- It must accepts a single parameter
- The parameter must not accept variable number of arguments and must have no default value
- It must be marked with **infix** keyword
  
  

```kt
class math {
    // user defined infix member function
    infix fun square(n : Int): Int{
        val num = n * n
        return num
    }
}
fun main(args: Array<String>) {
   val m = math()
    // call using infix notation
    val result = m square 3
    print("The square of a number is: "+result)
}
```

output :

```kt
The square of a number is: 9
```

Then, we create an object for the class **math()**   
and called the function using infix notation.



```kt
class check{
    // user defined infix member function
    infix fun dataType(x: Any):Any{
    var i = when(x){
            is String -> "String"
            is Int -> "Integer"
            is Double -> "Double"
            else -> "invalid"
        }
        return i
    }
}
fun main(args: Array<String>){
    var chk = check()
    // call using infix notation
    var result = chk dataType 3.3
    println(result)
}
```

the not correct way :

```kt
infix fun Int.add(a: Int, b: Int): Int // WRONG

infix fun Int.add(vararg numbers: Int): Int // WRONG

infix fun Int.add(value: Int = 10): Int // WRONG
```





resource:

[Kotlin infix function notation - GeeksforGeeks](https://www.geeksforgeeks.org/kotlin-infix-function-notation/)
