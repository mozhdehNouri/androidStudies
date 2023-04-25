runBlocking vs. coroutineScope:

Kotlin inDepth :

Create Custom Scope

every thing about job:

##### Error Handling :

first, you have to know about the cancellation concept and parent child relationship :

look at the example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

   val childJob1 : Job = launch {
      Log.d("TAG","childJob1")
    }

   val childJob2 :Job = launch {
   Log.d("TAG","childJob2")
    }  
}
}
```

output :

```kt
childJob1
childJob2
```

the following example child1 and child2 is childe of parent 

example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

   val childJob1 : Job = launch {
      Log.d("TAG","childJob1")
    }

   val childJob2 :Job = launch {
   Log.d("TAG","childJob2")
    }  
}
parentJob.cancell()
   Log.d("TAG","parentJob cancelled")
}
```

output :

```kt
parentJob cancelled
```

example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

   val childJob1 : Job = launch {
      delay(1000)
      Log.d("TAG","childJob1")
    }

   val childJob2 :Job = launch {
    delay(4000)
   Log.d("TAG","childJob2")
    }  
}
parentJob.cancell()
   delay(2000)
   Log.d("TAG","parentJob cancelled")
}
```

output :

```kt
childJob1
parentJob cancelled
```

explation :

childJob2 waiting for 4 second but after 2 second parent will be cancell 

concept behind here is  if parent coroutine is getting canceled all the childe coroutine inside parent coroutine will automatically canceled

if any coroutine gets canceled , it throws cancellation exception

lets take another example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

   val childJob1 : Job = launch {
      delay(1000)
      Log.d("TAG","childJob1")
    }

   val childJob2 :Job = launch {
    delay(4000)
   Log.d("TAG","childJob2")
    }  

childJob1.cancel()
   Log.d("TAG","childJob1 got cancel")
}
}
```

output:

```kt
childJob1 got cancel
childJob2
```

example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

  launch {
     try{
  launch {
    throw Exception("Exception inside childe1")
    }
      Log.d("TAG","childJob1")
      }catch(e:Exception){
    Log.d("TAG","childJob1 got handel")
        }
    }
}
}
```

output :

```kt
Exception inside childJob1
```

explation :

the childe propagate that exception to the parent coroutine.

the above example when we throw exception "Exception inside childJob1"  its propagate to parent coroutine and parent not handling.

diffrent between Exception and cancellation Exception :

the last example use the Exception 

but now use CancelltionException 

example :

```kt
GlobalScope.launch{

val parentJob:Job = launch {

  launch {
     try{
  launch {
    throw CancellationException("Exception inside childe1")
    }
      Log.d("TAG","childJob1")
      }catch(e:Exception){
    Log.d("TAG","childJob1 got handel")
        }
    }
}
}
```

output :

```kt
nothing happend 
```

we are 2 kind of exception:

`caught `and **uncaught**

coroutine feature is handling the cancellation exception . and coroutine handle by itself and dont neet we are handle.

and its a caught Exception becuse its not print any thing in logcat and handled by coroutine.

use coroutine Exception handler :

example:

```kt
val coroutineExceptionHandler =CoroutineExceptionHandler {coroutineContext ,throwable->
Log.d(TAG,"coroutine exception handled $throwable)

}

GlobalScope.launch(coroutineExceptionHandler){

val parentJob:Job = launch {

  launch {

  launch {
    throw Exception("Exception inside childe1")
    }
    }
}
}
```

output :

```kt
nothing print in logcat
```

set `coroutineExceptionHandler ` to parent.

see another example :

```kt
val coroutineExceptionHandler = CoroutineExceptionHandler {coroutineContext ,throwable->
Log.d(TAG,"coroutine exception handled $throwable)
   }
CoroutineScope(coroutineExceptionHandler).launch{
launch{
delay(500)
throw Exception("first child Exception")    
}    

launch{
delay(700)
 Log.d("TAG","second Child")
}    
}
```

output :

```kt
first child Exception
```

note : if any child in corourinScope is failure coroutineScope will be  cancell and finally all childern in scope will canceled .

example :

if we have a case to run our api independently we use like this:

```kt
val coroutineExceptionHandler = CoroutineExceptionHandler {coroutineContext ,throwable->
Log.d(TAG,"coroutine exception handled $throwable)
   }
CoroutineScope(coroutineExceptionHandler).launch{
supervisorScope {
launch{
delay(500)
throw Exception("first child Exception")    
}    

launch{
delay(700)
 Log.d("TAG","second Child")
}    
}
}
```

output :

```kt
first child Exception
second child
```

another video :

look at the example :

```kt
lifecycleScope.launch{
try {
    launch{
 launch {
     throw Exception()
    }
     }    
  } catch(e:Exception){
    println("Caught exception: $e")
  }
```

output

```kt
Excption
```

answer : its propagated  Exception to parent and parent can not catch exception 

if you use code like below you can not see Exception 

```kt
lifecycleScope.launch{
    launch{
 launch {
     try{
     throw Exception()
    }
   } catch(e:Exception){
    println("Caught exception: $e")
   }
     }    
```

in async block you don't see unless the await() function is called.

look at the example :

```kts
lifecycleScope.launch{
val number :String = async {
 delay(500)
throw Exception("error exception")  
"Result"
    }
 }
```

output :

```kt
Crash  error exception  
```

why?

because async propagate crash to up and launch can not handle the exception.

look at this example  :

```kt
lifecycleScope.async {
val number :String = async {
 delay(500)
throw Exception("error exception")  
"Result"
    }
 }
```

output :

```kt
nothing 
```

look at await with launch :

```kt
val deferred =lifecycleScope.async {
val number :String = async {
 delay(500)
throw Exception("error exception")  
"Result"
    }
 }
lifecycleScope.launch{
    deferred.await()
}
```

output :

```kt
crash
```

because call await and await can't habdle the exception and finally propagate to up and finally launch crash.

you can handle  previous example like this :

```kt
val deferred =lifecycleScope.async {
val number :String = async {
 delay(500)
throw Exception("error exception")  
"Result"
    }
 }
lifecycleScope.launch{
try {
    deferred.await()
}catch(e:Exception){
    e.printStackTrace()
}
}
```

output :

```kt
nothing
```

note : if you handle you exception like above you solution is wrong .

don't catch you exception like above.

you have to working like this :

##### CoroutineExceptionHandler:

with CoroutineExceptionHandler you can handle uncaught Exception.

example :

```kt
val handler =CoroutineExceptionHandler{_,throwable->
println("caught exception: $throwable")    
}
lifecycleScope.launch(handle){
launch{
    throw Exception()
}
}
```

output :

```kt
print caught Exception 
```

cancellationException is handle by coroutine so you with CoroutineExceptionHandler you can uncaught exception handled not caught like cancellation exception because cancellation exception handle automaticlly by coroutine.

we have 2 type scope:

1- normal coroutine scope like lifecycle and ViewModel ...

2 - supervisor scope.

note : difference between  launch {} and CoroutineScope.launch{ } : 

Both `launch {}` and `CoroutineScope(Dispatcher.Main).launch {}` are used to launch a new coroutine in Kotlin, but there is a subtle difference between them.

`launch {}` is a shorthand for `GlobalScope.launch {}`. It launches a new coroutine in the global scope, which means that the coroutine is not tied to any specific lifecycle, such as an activity or a fragment. This can lead to memory leaks and other issues if the coroutine is not properly managed.

On the other hand, `CoroutineScope(Dispatcher.Main).launch {}` creates a new coroutine scope that is tied to the main thread dispatcher. This means that the coroutine will be automatically cancelled when the scope is cancelled, such as when the associated activity or fragment is destroyed. It also allows for better control and management of the coroutine's lifecycle, since the scope can be cancelled explicitly.

In summary, it is generally recommended to use `CoroutineScope(Dispatcher.Main).launch {}` over `launch {}` to ensure proper lifecycle management of coroutines.

practice: try this code and answer the question

 this coroutine run whitch thread?

```kt
     CoroutineScope(Dispatchers.Main).launch(Dispatchers.IO) {

       }
```

##### coroutineScope with supervisor Scope :

```kt
val handler = CoroutineExceptionHandler{-,throwable->
println("caught exception $throwable")    

CoroutineScope(Dispatcher.Main).launch(handler ) {

launch{
    delay(3000M)
throw Exception("coroutine 1 failed")
}
launch{
    delay(4000M)    
   println("coroutine2  is finished)
}
}
}
```

output :

```kt
just printetd coroutine 1 failed"
```

explain: program did'nt crash just print `coroutine 1 failed` but child2 not launch so what happend here? and 2 coroutine running independently of each other and seems first launch effect second launch .

no matter if you use coroutineHandlerException or not it will cancell all  child coroutine and whole scope.

 becuase we use coroutine scope. 

so you can use supervisor scope :

```kt
val handler = CoroutineExceptionHandler{-,throwable->
println("caught exception $throwable")    

CoroutineScope(Dispatcher.Main+ handler).launch {
    supervisorScope{
launch{
    delay(3000M)
throw Exception("coroutine 1 failed")
}
launch{
    delay(4000M)    
   println("coroutine2  is finished")
}
}
}
}
```

output :

```kt
coroutine 1 failed
coroutine2  is finished
```

example : look at the example and say your answer

common mistake is using try/catch block.

```kt
lifecycleScope.launch{
val job= launch {
    try {
delay(500L)
}catch(e:Exception){
    e.printStackTrace()
}
println("coroutine 1 finished")
}    
delay(300L)
job.cacel()
}
```

output :

```kt
cancellationException
coroutine 1 finished()
```

###### Best practices for kotlin coroutine :

Inject Dispatchers :

Don't hardcode `Dispatchers` when creating new coroutines or calling `withContext`.

- you should inject distpacher

- This dependency injection pattern makes testing easier

```kt
// DO inject Dispatchers
class NewsRepository(
    private val defaultDispatcher: CoroutineDispatcher = Dispatchers.Default
) {
    suspend fun loadNews() = withContext(defaultDispatcher) { /* ... */ }
}

// DO NOT hardcode Dispatchers
class NewsRepository {
    // DO NOT use Dispatchers.Default directly, inject it instead
    suspend fun loadNews() = withContext(Dispatchers.Default) { /* ... */ }
}
```

###### what is job Exactlly ?

first we need  to learning parent-childe  role relationShip :

  1- children inherit context from their parent;

```kt
fun main(): Unit = runBlocking(CoroutineName("main")) {
    val name = coroutineContext[CoroutineName]?.name
    println(name) // main
    launch {
        delay(1000)
        val name = coroutineContext[CoroutineName]?.name
        println(name) // main
    }
}
```

2- a parent suspends until all the children are finished

3- when the parent is cancelled, its child coroutines are also cancelled;

4 - when a child is destroyed, it also destroys the parent

`Job` can be used to cancel coroutines, track their state, and much more.

what is job ?

 a job represents a cancellable thing with a lifecycle.A job lifecycle is represented by its state.

![a](C:\Users\mozhdeh.nouri\Desktop\coroutine_state_diagram.jpg)

In the "Active" state, a job is running and doing its job. Most coroutines will start in the "Active" state. Only those that are started lazily will start with the "New" state (which means it does not start automatically. ).These need to be started in order for them to move to the "Active" state.

When it is done, its state changes to "Completing" where it waits for its children.

 Once all its children are done, the job changes its state to "Completed"

 if a job cancels or fails when running (in the "Active" or "Completing" state), its state will change to "Cancelling"

```kt
import kotlinx.coroutines.*

suspend fun main() = coroutineScope {
    // Job created with a builder is active
    val job = Job()
    println(job) // JobImpl{Active}@ADD
    // until we complete it with a method
    job.complete()
    println(job) // JobImpl{Completed}@ADD

    // launch is initially active by default
    val activeJob = launch {
        delay(1000)
    }
    println(activeJob) // StandaloneCoroutine{Active}@ADD
    // here we wait until this job is done
    activeJob.join() // (1 sec)
    println(activeJob) // StandaloneCoroutine{Completed}@ADD

    // launch started lazily is in New state
    val lazyJob = launch(start = CoroutineStart.LAZY) {
        delay(1000)
    }
    println(lazyJob) // LazyStandaloneCoroutine{New}@ADD
    // we need to start it, to make it active
    lazyJob.start()
    println(lazyJob) // LazyStandaloneCoroutine{Active}@ADD
    lazyJob.join() // (1 sec)
    println(lazyJob) //LazyStandaloneCoroutine{Completed}@ADD
}
```

exp : each coroutine has its own job

##### another definition of job :

A Job is a cancellable thing with a life-cycle that culminates in its completion.Coroutine job is created with launch coroutine builder. It runs a specified block of code and completes on completion of this block.

###### how we can get the job?

 a job will be returned when a new coroutine will be launched it means when we use launch actully we have a job and we can save in variable .

```kt
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.GlobalScope
import kotlinx.coroutines.launch

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // A job is returned
        val job = GlobalScope.launch(Dispatchers.Default) {

        }
    }
}
```

things which can done using job 

- **start()**
- **join()**
- **cancel()**

what is join :

join function is a suspending function. it can be called form a coroutine or from another suspend function.

Job blocks all the threads until the coroutine in which it is written or have context finished its work.  Only when the coroutine gets finishes, lines after the **join()** function will be executed.

The first important advantage of a job is that it can be used to wait until the coroutine is completed. For that, we use the `join` method. This is a suspending function that suspends until a concrete job reaches a final state (either Completed or Cancelled).

```kt
import kotlinx.coroutines.*

fun main(): Unit = runBlocking {
    val job1 = launch {
        delay(1000)
        println("Test1")
    }
    val job2 = launch {
        delay(2000)
        println("Test2")
    }

    job1.join()
    job2.join()
    println("All tests are done")
}
// (1 sec)
// Test1
// (1 sec)
// Test2
// All tests are done
```

we can wait for it to finish with join function.

```kt
// sample kotlin program for demonstrating job.join method
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import kotlinx.coroutines.*

class MainActivity : AppCompatActivity() {

    val TAG:String = "Main Activity"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // creating/launching a coroutine will return the job
        val job = GlobalScope.launch(Dispatchers.Default) {
            repeat(5)
            {
                Log.d(TAG, "Coroutines is still working")
                // delay the coroutine by 1sec
                delay(1000)
            }
        }

        runBlocking {
              // waiting for the coroutine to finish it's work
            job.join()
            Log.d(TAG, "Main Thread is Running")
        }
    }
}
```

example :

```kt
import kotlinx.coroutines.*

fun main(): Unit = runBlocking {
    launch {
        delay(1000)
        println("Test1")
    }
    launch {
        delay(2000)
        println("Test2")
    }

    val children = coroutineContext[Job]
        ?.children

    val childrenNum = children?.count()
    println("Number of children: $childrenNum")
    children?.forEach { it.join() }
    println("All tests are done")
}
// Number of children: 2
// (1 sec)
// Test1
// (1 sec)
// Test2
// All tests are done
```

##### cancel :

**cancel()** method is used to cancel the coroutine, without waiting for it to finish its work. It can be said that it is just opposite to that of the join method, in the sense that **join()** method waits for the coroutine to finish its whole work and block all other threads, whereas the **cancel()** method when encountered, kills the coroutine ie stops the coroutine.

```kt
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import kotlinx.coroutines.*

class MainActivity : AppCompatActivity() {

    val TAG:String = "Main Activity"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val job = GlobalScope.launch(Dispatchers.Default) {
            repeat(5)
            {
                Log.d(TAG, "Coroutines is still working")
                delay(1000)
            }
        }

        runBlocking {
              // delaying the coroutine by 2sec
            delay(2000)

            // cancelling/stopping  the coroutine
            job.cancel()
            Log.d(TAG, "Main Thread is Running")
        }
    }
}
```

output :

```kt
starting for long calculation
result for i =30
result for i =31
result for i =32
result for i =33
result for i =34
result for i =35
result for i =36
jkhjhgf
fjkghfffh
result for i =37
result for i =38
result for i =39
result for i =40
```

explain output :

It can be seen that even after the **cancel()** method has been encountered, our coroutine will continue to calculate the result of the Fibonacci of the numbers. It is so because our coroutine was so busy in doing the calculation that it does not get time to cancel itself. Suspending there has no suspended function(like **delay()**), we don’t have enough time to tell the coroutine that it has been canceled. So we have to check manually that if the coroutine has been canceled or not. 

This can be done using **isActive** to check whether the coroutine is active or not.

```kt
// sample kotlin program
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import kotlinx.coroutines.*

class MainActivity : AppCompatActivity() {

    val TAG:String = "Main Activity"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val job = GlobalScope.launch(Dispatchers.Default) {
            Log.d(TAG, "Starting the long calculation...")
            for(i in 30..40)
            {
                  // using isActive functionality to check whether the
                  // coroutine is active or not
                if(isActive)
                Log.d(TAG, "Result for i =$i : ${fib(i)}")
            }
            Log.d(TAG, "Ending the long calculation...")
        }

        runBlocking {
            delay(2000)
            job.cancel()
            Log.d(TAG, "Main Thread is Running")
        }
    }

    fun fib(n:Int):Long
    {
        return if(n==0) 0
        else if(n==1) 1
        else fib(n-1) + fib(n-2)
    }
}
```

please check the out put :

###### withTimeOut :

the coroutine will automatically be canceled and don’t do any further calculations when a certain time has elapsed and **withTimeOut()** helps in doing so. There is no need to cancel the coroutine manually by using **runBlocking()** function. Let’s see how **withTimeOut()** function works:

```kt
// sample kotlin program
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.util.Log
import kotlinx.coroutines.*

class MainActivity : AppCompatActivity() {

    val TAG:String = "Main Activity"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val job = GlobalScope.launch(Dispatchers.Default) {
            Log.d(TAG, "Starting the long calculation...")

            // using withTimeOut function
            // which runs the coroutine for 3sec
            withTimeout(3000L)
            {
                for(i in 30..40)
                {
                    if(isActive)
                        Log.d(TAG, "Result for i =$i : ${fib(i)}")
                }
            }
            Log.d(TAG, "Ending the long calculation...")
        }

    }

    fun fib(n:Int):Long
    {
        return if(n==0) 0
        else if(n==1) 1
        else fib(n-1) + fib(n-2)
    }
}
```

###### supervisor Job:

When a coroutine is launched inside a supervisor job, it becomes a child of that job, and if any of those child coroutines fail due to an exception, the supervisor job will not propagate that exception to its parent or siblings. Instead, the supervisor job will try to handle the exception based on its own error handling strategy.

The supervisor job can have its own exception handler that can be used to handle any exceptions that occur in the child coroutines. This allows the supervisor job to recover from those exceptions and continue executing the remaining child coroutines.

Using a supervisor job can be particularly useful in scenarios where a group of coroutines need to run independently but still be coordinated and monitored. 

For example, if you are downloading several files concurrently, and one of the downloads fails due to a network error, you might want to retry that download or continue downloading the remaining files instead of stopping the entire process.

To create a supervisor job, you can use the `SupervisorJob` factory function, like this:

```kt
val supervisorJob = SupervisorJob() 
val coroutineScope = CoroutineScope(Dispatchers.Default + supervisorJob)
```

This creates a supervisor job and a new coroutine scope that uses that job as its parent. You can then launch child coroutines inside that scope, and they will become children of the supervisor job.

read this article and transform here 

[Difference Between Coroutine Scope and Coroutine Context | Baeldung on Kotlin](https://www.baeldung.com/kotlin/coroutines-scope-vs-context)

and read this resource:

https://developer.android.com/kotlin/coroutines/additional-resources

[Best practices for coroutines in Android &nbsp;|&nbsp; Kotlin &nbsp;|&nbsp; Android Developers](https://developer.android.com/kotlin/coroutines/coroutines-best-practices)

https://medium.com/androiddevelopers/coroutines-first-things-first-e6187bf3bb21

https://medium.com/androiddevelopers/coroutines-on-android-part-i-getting-the-background-3e0e54d20bb

[Use Kotlin Coroutines in your Android App &nbsp;|&nbsp; Android Developers](https://developer.android.com/codelabs/kotlin-coroutines#0)

https://github.com/amitshekhariitbhu/Learn-Kotlin-Coroutines

reference :

https://www.youtube.com/watch?v=zV7krCaslYg 

[In-Depth Guide to Coroutine Cancellation &amp; Exception Handling - Android Studio Tutorial - YouTube](https://www.youtube.com/watch?v=VWlwkqmTLHc)

https://developer.android.com/kotlin/coroutines/coroutines-best-practices

[Jobs, Waiting, Cancellation in Kotlin Coroutines - GeeksforGeeks](https://www.geeksforgeeks.org/jobs-waiting-cancellation-in-kotlin-coroutines/)

[Job and children awaiting in Kotlin Coroutines](https://kt.academy/article/cc-job)

https://www.youtube.com/watch?v=55W60o9uzVc
