this article every thing about kotlin keywords :

#### what is `inline `function :

- inline functions in Kotlin help us to avoid extra memory allocations and unnecessary method invocations for each lambda expression.

- higher-order functions or lambda expressions, all stored as an **object** so memory allocation, and virtual calls might introduce runtime overhead. Sometimes we can eliminate the memory overhead by inlining the lambda expression. In order to reduce the memory overhead of such higher-order functions or lambda expressions, we can use the **inline** keyword which ultimately requests the compiler to not allocate memory and simply copy the inlined code of that function at the calling place

- or another difinition  inline function instruct compiler to insert the complete body of the function wherever that function gets used in the code .

##### let's start with simple example :

both are normal functions. `guide()` function calls the `teach()` function.

example 1 :

```kotlin
fun guide(){
    println("before teach")
    teach()
    println("after teach")
}

fun teach(){
    println("teach")
}
```

let's see the decompiled code:

```kotlin
public voide guide():{
    system.out.print("before teach")
     teach()
     system.out.print("after teach")
}

public void teach(){
    system.out.print("teach")
}
```

##### look at `teach()` in guide function:

now add inline keyword to teach function:

```kotlin
fun guide(){
    println("before teach")
    teach()
    println("after teach")
}

inline fun teach(){
    println("teach")
}
```

##### kotlin byteCode :

```kotlin
public voide guide():{
    system.out.print("before teach")
    system.out.print("teach")
    system.out.print("after teach")
}
```

Now, we can see that the code of `teach()` function is copied inside the `guide()` function. And the `guide()` function is no more calling the `teach()` function.This is because we have used the `inline` keyword.

##### let's take an example with Higher-Order Function and Lambdas:

example 2 :

```kotlin
fun higherfunc( str : String, mycall :(String)-> Unit) {

    // inovkes the print() by passing the string str
    mycall(str)
}

// main function
fun main(args: Array<String>) {
    print("GeeksforGeeks: ")
    higherfunc("A Computer Science portal for Geeks",::print)
}
```

##### kotlin byteCode :

```kotlin
public static final void hicherfunc(@NctNuil String str,
 @NotNull Functionl mall) [
 Intrinsics.checkfarameterIsNoMull(str,paramName: "str"); 
Intrinsics.checkFarameterIsNotNull(mycall, paramName: "mall"); 
mycall.invoke(str); 

public star i-: final void main(U 1 String[] args) 
I Intrinsics.checkParameterIsNotNull(args, paramName: "ergs");
 String varl = "GeeskforGeeks: ";
 boolean var2 = false; System.out.print(varl); 
higherfunc( str: "A Computer Science portal for Geeks", 
(Functionl)ial.INSTANCE); 
```

In the above bytecode, the main part to focus on is: `mycall.invoke(str)`

*mycall* invokes the print function by passing the string as a parameter. While invoking the print(), it would create an additional call and increases memory overhead.

##### **It works like**

```kotlin
mycall(new Function() {
        @Override
        public void invoke() {
         //println statement is called here.
        }
    });
```

##### now with inline function :

```kotlin
inline fun higherfunc( str : String, mycall :(String)-> Unit){
    // inovkes the print() by passing the string str
    mycall(str)
}
// main function
 fun main(args: Array<String>) {
    print("GeeksforGeeks: ")
    higherfunc("A Computer Science portal for Geeks",::print)
}
```

##### and byteCode :

```kotlin
public static Wiiiv void hignerfunc(@: String str,
 @NocNull Functionl mvcal) 
I int Wiatalgherfunc = 0; 
Intrinsics.checkParameterIsNotNull(str, paramName: "str");
 Intrinsics.checkParameterIsNotNull(mycall, paramName: "mall");
 mycall.invoke(str); 

public static finial void main(r,LI String[] args) {
 Intrinsics.checkParameterIsNotNull(args, paramName: "args")
; String str$iv = "GeeskforGeeks: ";
 boolean quelotau = false;
 System.out.print(str$iv); 
str$iv = "A Computer Science portal for Geeks";
 gaiMbutra = false; 
 int var4 =false;
 boolean vary = false;
 System.out.print(str$iv); } 
```

##### example 3:

```kotlin
fun guide() {
    print("guide start")
    teach {
        print("teach")
    }
    print("guide end")
}

fun teach(abc: () -> Unit) {
    abc()
}
```

Again, let's go to the decompiled code. The decompiled code is as below:

```kotlin
public void guide() {
    System.out.print("guide start");
    teach(new Function() {
        @Override
        public void invoke() {
            System.out.print("teach");
        }
    });
    System.out.print("guide end");
}

public void teach(Function abc) {
    abc.invoke();
}
```

lets use inline keyword:

```kotlin
fun guide() {
    print("guide start")
    teach {
        print("teach")
    }
    print("guide end")
}

inline fun teach(abc: () -> Unit) {
    abc()
}
```

deCompile Code:

```kotlin
public void guide() {
    System.out.print("guide start");
    System.out.print("teach");
    System.out.print("guide end");
}
```

example 4 :

```kotlin
  fun higherOrderFunction(aLambda: () -> Unit) {

        doSomething()
        aLamda()
        doAnotherThing()

    }
```

converted to java:

```kotlin
public void higherOrderFunction(Function aLambda) {

    doSomething();
    aLambda.invoke();//invoke will execute the logic in your lambda.
    doAnotherThing();

}
```

This means that anytime you use the higher order function in your kotlin code, the Java translated code will end up looking something like this:

```kotlin
public void callingFunction(){ 
    higherOrderFunction(new Function() {
        @Override
        public void invoke() {
          //aLambda's logic here.  

        }
    });
}
```

Imagine calling this lambda in a loop. You’ll end up creating N `Function` objects.

##### with inline keyword :

```kotlin
//add the inline keyword to improve the higher order function's performance.
inline fun higherOrderFunction(aLambda: () -> Unit) {

    doSomething()
    aLamda()
    doAnotherThing()

}

fun callingFunction() {

  higherOrderFunction{ print("lambda logic") }

}
```

converted to java :

```kotlin
//note that the entire `higherOrderFunction` method
//was copied into this method during the inlining.
public void callingFunction() {

  doSomething()
  System.out.print("lambda logic")
  doAnotherThing()

}
```

point : also, when using inline functions, you will be able to return from the lambda which in turn will return from the calling function. This is usually called

 `non-local control flow.`

example :

```kotlin
inline fun higherOrderFunction(aLambda: () -> Unit) {
    aLambda()
    print("I won't be executed when you call callingFunction()")
}

fun callingFunction() {
    higherOrderFunction {
       print("Non-local control flow")
        return
    }
}
```

With the help of the **inline** keyword, the **println** lambda expression is copied in the main function in the form of System.out.println and no further calls required.

advantage of inline function : Function call overhead dosen't occur. less overhead and faster program execution.

so when to make the function inline and when not :

- when the function code is very small , its good a idea to make the function inline.

- when the function code is large and called from so many places its ga bad idea to make the function inlinw, as the large code will be repeates again and again.



remmebring : you cant lambda returning but when using inline key word you can non-local return 

# Noinline:

Say you have multiple lambdas in your inlined function and you don’t want all of them to be inlined, you can mark the lambdas you don’t want to be inlined with the `noinline` keyword:

example :

```kotlin
inline fun higherOrderFunction(aLambda: () -> Unit,
 noinline dontInlineLambda: () -> Unit,
 aLambda2: () -> Unit) {

    doSomething()

    aLambda()
    dontInlineLambda()//won't be inlined.
    aLambda2()

    doAnotherThing()

}
```

Note that `noinline` lambdas do not support non-local control flow,ou won’t be able to propagate your return to the calling function.

another example :

```kotlin
fun guide() {
    print("guide start")
    teach({
        print("teach abc")
    }, {
        print("teach xyz")
    })
    print("guide end")
}

inline fun teach(abc: () -> Unit, noinline xyz: () -> Unit) {
    abc()
    xyz()
}
```

decomplie code :

```kotlin
public void guide() {
    System.out.print("guide start");
    System.out.print("teach abc");
    teach(new Function() {
        @Override
        public void invoke() {
            System.out.print("teach xyz");
        }
    });
    System.out.print("guide end");
}

public void teach(Function xyz) {
    xyz.invoke();
}
```

# Crossinline:

`crossinline` in Kotlin is used to avoid non-local returns.

so crossinline allow you inline body of lambda but with out allow you using none-local returns.

loock at this example:

```kotlin
fun guide() {
    print("guide start")
    teach {
        print("teach")
        return
    }
    print("guide end")
}

inline fun teach(abc: () -> Unit) {
    abc()
}
```

we have added a return statement in the lambda function that we are passing.

and deCompile code is :

```kotlin
public void guide() {
    System.out.print("guide start");
    System.out.print("teach");
}
```

we find that there is no `System.out.print("guide end")`  .As we have added the **return** inside the lambda, it allowed the **non-local returns** and left the code below that.

How can we avoid this situation?

`crossinline` in Kotlin is the solution to avoid non-local returns

**When we add the `crossinline`, then it will not allow us the put the return inside that lambda.**

example :

```kotlin
fun guide() {
    print("guide start")
    teach {
        print("teach")
        // return is not allowed here
    }
    print("guide end")
}

inline fun teach(crossinline abc: () -> Unit) {
    abc()
}
```

decompile :

```kotlin
public void guide() {
    System.out.print("guide start");
    System.out.print("teach");
    System.out.print("guide end");
}
```

Resource :

- [Kotlin Inline Functions - GeeksforGeeks](https://www.geeksforgeeks.org/kotlin-inline-functions

- https://www.youtube.com/watch?v=GLLI8h67ryo&list=PL_I3TGB7aK6jNBMZkw3FYdJXyf7quHdI8

- https://medium.com/android-news/inline-noinline-crossinline-what-do-they-mean-b13f48e113c2

- [Difference Between crossinline and noinline in Kotlin | Baeldung on Kotlin](https://www.baeldung.com/kotlin/crossinline-vs-noinline)

- [inline function in Kotlin](https://amitshekhar.me/blog/inline-function-in-kotlin)

- [noinline in Kotlin](https://amitshekhar.me/blog/noinline-in-kotlin)

- [crossinline in Kotlin](https://amitshekhar.me/blog/crossinline-in-kotlin)
