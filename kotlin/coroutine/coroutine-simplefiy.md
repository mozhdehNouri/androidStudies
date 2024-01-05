Simplifying android Development with coroutine

most apps retrieve data from or send data to a local database or a backend server.
These tasks and other resource-intensive computations may happen instantly or take a while to
finish.

Long-running operations must not be performed on the main UI thread as the application will be
blocked until they are completed. The application might become unresponsive to the users. Users may
not be aware of what’s happening, and this might prompt them to close the app and reopen it  (
canceling the original task or doing it again). The app can also suddenly crash. Some users might
even stop using your app if this happens frequently.

To prevent this from happening, you need to use asynchronous programming. Tasks that can take an
indefinite amount of time must be done asynchronously. They must run in the background, parallel to
other tasks.

#### Understanding asynchronous programming

Asynchronous programming is a programming method that allows work to be done independently of the
main application thread

You can run a task that may be processed indefinitely on a background thread (in parallel to the
main thread) without freezing the app. This will allow the users to still interact with the app or
the UI while the original task is running. When the task has
finished or if an error was encountered, you can then inform the user using the main thread.

#### The new way to do it – coroutines and flows

Coroutines is a Kotlin library you can use in Android to perform asynchronous tasks. Coroutines is a
library for managing background tasks that return a single value.
Flows are built on top of coroutines that can return multiple values.

Coroutines is a Kotlin library for managing background tasks, such as making network calls and
accessing files or databases, or performing long-running background tasks. Using Kotlin coroutines
is Google’s official recommendation for asynchronous programming on Android. Their Android Jetpack
libraries, such as Lifecycle, WorkManager, and Room-KTX, now include support for coroutines. Other
Android libraries, such as Retrofit, Ktor, and Coil, provide first-class support for Kotlin
coroutines.

With Kotlin coroutines, you can write your code in a sequential way. A long-running task can be made
into a suspend function. A suspend function is a function that can perform its task by suspending
the thread without blocking it, so the thread can still run other tasks. When the suspending
function is done, the current thread will resume execution. This makes the code easier to read,
debug, and test. Coroutines follow a principle of structured concurrency

With Kotlin coroutines, you can write your code in a sequential way. A long-running task can be made
into a suspend function. A suspend function is a function that can perform its task by suspending
the thread without blocking it, so the thread can still run other tasks. When the suspending
function is done, the current thread will resume execution. This makes the code easier to read,
debug, and test. Coroutines follow a principle of structured concurrency.

---

coroutine :

When you create a coroutine inside another coroutine, the new coroutine becomes the child of the
original coroutine. The original coroutine becomes the parent of the new coroutine. This can be seen
in the following code

```kt
CoroutineScope(Dispatchers.IO).launch {
 performTask1()
 launch {
 performTask2()
 }
 ... 
}
```

#### Coroutine builders:

Coroutine builders are the functions that you can use to create coroutines. To create
a coroutine, you can use the following Kotlin coroutine builders:
• launch
• async
• runBlocking

async and launch need to be started on a coroutine scope. Meanwhile, runBlocking doesn’t need to be
started from a coroutine scope.
The launch keyword creates a coroutine and doesn’t return a value. Instead, it returns a Job object
that represents the coroutine

The launch coroutine builder is ideal to use when you want to run a task and then forget about it (
this means you are not waiting for the result of the operation).

```kt
class MainActivity : AppCompatActivity() {
 val scope = MainScope()
 override fun onCreate(savedInstanceState: Bundle?) {
 super.onCreate(savedInstanceState)
 setContentView(R.layout.activity_main)
 val progressBar =
 findViewById<ProgressBar>(R.id.progressBar)
 scope.launch {
 delay(1_000)
 progressBar.isVisible = true
 }
 }
}
```

the async builder is similar to launch but it returns a value: a Deferred object. Later, you can get
this value with the await function. The async builder should be used when you want to execute a task
and want to get the output of said task

runBlocking starts a new coroutine and blocks the current thread until the task has been executed.
This is useful for cases when you need to block the thread. Creating unit tests is one of these
cases:

#### Coroutine scopes:

CoroutineScope is the scope with which the coroutine will run. It defines the life cycle of the
coroutines created from it, from its start to its end. If you cancel a scope, it will cancel all the
coroutines it created.

The launch and async coroutine builders are extension functions from CoroutineScope
for creating coroutines.

MainScope is the main CoroutineScope for the main thread, which uses Dispatchers.
Main for its coroutine. It is normally used for creating coroutines that will update the user
interface.

You can also create a CoroutineScope instead of using MainScope by creating one with the
CoroutineScope factory function. The CoroutineScope function requires you to pass in a coroutine
context. CoroutineContext is a collection of elements for the coroutines that specify how the
coroutine should run.

You passed a dispatcher and both a dispatcher and a job in the previous examples for the coroutine
context. Dispatchers and jobs are coroutine context elements.

Your CoroutineScope must have a job and a way for the coroutine to be canceled, such as when
Activity, Fragment, or ViewModel has been closed

lifecycleScope is a CoroutineScope from Jetpack’s Lifecycle library that you can use to
create coroutines. It is tied to the Lifecycle object (similar to your activity or fragment) and is
automatically canceled when the life cycle is destroyed. Thus, you no longer need to manually cancel
them. lifecycleScope simplifies how scopes are created, how jobs are handled, and how they can be
canceled within your activity or fragment. A lifecycleScope uses Dispatchers.Main.immediate for its
dispatcher and a SupervisorJob for its job, such as viewModelScope

To change the dispatcher that the coroutine will use, you can pass in a dispatcher when using the
launch and async coroutine builders:

```kt
lifecycleScope.launch(Dispatchers.IO) { ... }
```

This will use the Dispatchers.IO dispatcher instead of the lifecycleScope object's default
Dispatchers.Main.immediate for the coroutine that was launched.
Aside from launch, lifecycleScope has additional coroutine builders, depending on the
life cycle’s state:
**• launchWhenCreated
• launchWhenStarted
• launchWhenResumed**

As the name suggests, launchWhenCreated launches the coroutine when the life cycle is created,
launchWhenStarted launches the coroutine when the life cycle is started, and launchWhenResumed
launches the coroutine when the life cycle goes back to the Resumed state.

**viewModelScope**
viewModelScope is the ViewModel’s default CoroutineScope for creating coroutines. It is ideal to use
if you need to do a long-running task from ViewModel. This scope and all running jobs are
automatically canceled when ViewModel is cleared (that is, when onCleared is invoked).
viewModelScope simplifies the creation of Scope, handling the job, and canceling within ViewModel. A
viewModelScope uses Dispatchers.Main.immediate for its dispatcher and uses a SupervisorJob for the
job. A SupervisorJob is a special version of Job that allows its children to fail independently of
each other.

#### coroutineScope{} and supervisorScope{}

The coroutineScope{} suspending builder allows you to create a CoroutineScope with
the coroutine context from its outer scope. This calls the code block inside and does not complete
until everything is done.

When a child coroutine fails, it will cancel the parent coroutine and the sibling coroutines. If you
do not want this to happen, you can use supervisorScope{} instead of coroutineScope{}

When a child coroutine fails, the parent and sibling coroutines will continue to work and will not
be affected.

##### GlobalScope

GlobalScope is a special CoroutineScope that is not tied to an object or a job. It should
only be used in cases when you must run a task or tasks that will always be active while the
application is alive. As such, if you want to use GlobalScope, you must annotate the call with
@OptIn(DelicateCoroutinesApi::class).

##### Coroutine dispatchers

Coroutines have a context, which includes the coroutine dispatcher. Dispatchers specify what thread
the coroutine will use to perform the task.

Dispatchers.Main: This is used to run on Android’s main thread, usually for updates
to the user interface. A special version of Dispatchers.Main, called Dispatchers.
Main.immediate, is used to immediately execute the coroutine in the main thread. The
viewModelScope and lifecycleScope coroutine scopes use Dispatchers. Main.immediate by default.

• Dispatchers.IO: This is designed for networking operations, and for reading from or
writing to files or databases.

• Dispatchers.Default: This is used for CPU-intensive work, such as complicated
computations or processing text, images, or videos. If you don’t set a dispatcher,
Dispatchers.Default will be chosen by default.

• Dispatchers.Unconfined: This is a special dispatcher that is not confined to any
specific threads. It executes the coroutine in the current thread and resumes it in whatever thread
that is used by the suspending function

To change the context of your coroutine, you can use the withContext function for the code that you
want to use a different thread with. For example, in your suspending function, getMovies, which gets
movies from your endpoint, you can use Dispatchers.IO:

```kt
suspend fun getMovies(): List {
 withContext(Dispatchers.IO) { ... }
}
```

In the preceding code, the getMovies function uses Dispatchers.IO to fetch the list of
movies from a network endpoint

#### Understanding coroutine contexts and jobs

Coroutines run in a coroutine context. A job is the context of the coroutine that allows you to
manage the coroutine’s execution

##### Coroutine contexts

Each coroutine runs in a coroutine context. A coroutine context is a collection of elements for the
coroutines that specifies how the coroutine should run. A coroutine scope has a default coroutine
context; if it’s empty, it will have an EmptyCoroutineContext. When you create a CoroutineScope or
use a coroutine builder, you can pass in a CoroutineContext. In the previous examples, we were
passing a dispatcher:

```kt
CoroutineScope(Dispatchers.IO) {
 …
}
viewModelScope.launch(Dispatchers.Default) { ... }
```

What you’re passing in these functions is a CoroutineContext. The following are some of the
CoroutineContext elements you can use:
• CoroutineDispatcher
• Job
• CoroutineName
• CoroutineExceptionHandler

The main CoroutineContext elements are dispatchers and jobs. Dispatchers specify the thread where
the coroutine runs, while the job of the coroutine allows you to manage the coroutine’s task

Jobs allow you to manage the life cycle of the coroutine, from the creation of the coroutine to the
completion of the task. You can use this job to cancel the coroutine itself.

##### Coroutine jobs

A job is a ContextCoroutine element that you can use for the coroutine context. You can use jobs to
manage the coroutine’s tasks and its life cycle. Jobs can be canceled or joined together.

The launch coroutine builder creates a new job, while the async coroutine builder
returns a Deferred object. Deferred is itself a Job object – that is, a job that has
a result

To access the job from the coroutine, you can set it to a variable:

```kt
val job = viewModelScope.launch(Dispatchers.IO) { ... }
```

The launch coroutine builder creates a coroutine running in the Dispatchers.IO thread and returns a
job. A job can have children jobs, making it a parent job. Job has a children property you can use
to get the job’s children :

```kt
val job1 = viewModelScope.launch(Dispatchers.IO) { 
 val movies = fetchMovies()
 val job2 = launch {

 }

}
```

In this example, job2 becomes a child of job1, which is the parent. This means that job2 will
inherit the coroutine context of the parent, though you can also change i

If a parent job is canceled or failed, its children are also automatically canceled. When a child’s
job is canceled or failed, its parent will also be canceled.

A SupervisorJob is a special version of a job that allows its children to fail independently of each
other

Using a job also allows you to create a coroutine that you can later start instead of
immediately running by default. To do this, you must use CoroutineStart.LAZY
as the value of the start parameter in your coroutine builder and assign the result to
a Job variable. Later, you can then use the start() function to run the coroutine,
as shown here.

```kt
val lazyJob = viewModelScope.launch (start=CoroutineStart.LAZY) 
{
 delay(1_000)
 ...
}
...
lazyJob.start()
```

This will create a lazy coroutine. When you are ready to start it, you can simply call
lazyJob.start(). With the Job object, you can also use the join() suspending function to wait for
the job to be completed before continuing with another job or task:

```kt
viewModelScope.launch {
 val job1 = launch {
 showProgressBar()
 }
 job1.join()

 val job2 = launch {
 fetchMovies()
 }
}
```

In this example, job1 will be run first and job2 won’t be executed until the former job (job1) has
finished.

**Coroutine job states**

A job has the following states:
• New
• Active
• Completing
• Completed
• Canceling
• Canceled

When you start a coroutine, a job is created in the New state. It then becomes Active when the job
automatically starts or when you call the start() or join() function. The job is in the Active state
while the job is running.

Completing a job moves it into the Completing state and then into the Completed state once its
children complete their tasks.
If a job is canceled manually or failed because of an exception, it will go into the Canceling state
andthen into the Canceled state once its children complete.
A Job object has three properties you can use to check the state of the job:

• isActive: This property is true when the job is running or completing, and
false otherwise.
• isComplete: This property is true when the job has finished its task (canceled or completed),
and false otherwise.
• isCancelled: This property is true if the job has been canceled or is being canceled,
manually or because of an exception, and false otherwise.

---

##### Handling Coroutine Cancelations and Exceptions

Coroutines can be canceled when their purpose has been fulfilled or their job has been done. You can
also cancel them based on specific instances in your app, such as when you want users to manually
stop a task with a tap of a button. Coroutines do not always succeed and can fail; developers must
be able to handle these cases so that the app will not crash, and they can inform the users by
displaying a toast or snackbar message.

##### Canceling coroutines:

If your application is doing a long-running operation that is taking longer than expected and you
think it could cause a crash, you might want to stop that task.

You can also end tasks that are no longer necessary to free up memory and resources, such as when
the user moves out of the activity that launched the task or closes the application. Users can also
manually discontinue certain operations if you have that feature in your application.

If you are using viewModelScope from ViewModel or lifecycleScope from
the Jetpack Lifecycle Kotlin extension libraries, you can easily create coroutines without
manually handling the cancelation. When ViewModel is cleared, viewModelScope
is automatically canceled, while lifecycleScope is automatically canceled when the lifecycle is
destroyed. If you created your own coroutine scope, you must add the cancelation yourself.

using coroutine builders such as launch returns a job. Using this job object, you can call the
cancel() function to cancel the coroutine.

```kt
class MovieViewModel: ViewModel() {
 init {
 viewModelScope.launch {
 val job = launch {
 fetchMovies()
 }
 ...
 job.cancel()
 }

```

The job.cancel() function will cancel the coroutine launched to call the fetchMovies()
function. After canceling the job, you may want to wait for the cancelation to be finished before
continuing to the next task to avoid race conditions. You can do that by calling the join function
after calling the call function

```kt
class MovieViewModel: ViewModel() {
 init {
 viewModelScope.launch() {
 val job = launch {
 fetchMovies()
 }
 job.cancel()
 job.join()
 hideProgressBar()
 }
 }
}
```

Adding job.join() here would make the code wait for the job to be canceled before doing the next
task, which is hideProgressBar().You can also use the Job.cancelAndJoin() extension function, which
is the same as calling cancel and then the join function.

If your coroutine scope has multiple coroutines and you need to cancel all of them, you can use the
cancel function from the coroutine scope instead of canceling the jobs one by one. This will cancel
all the coroutines in the scope.

```kt
class MovieViewModel: ViewModel() {
 private val scope = CoroutineScope(Dispatchers.Main +
 Job())
 init {
 scope.launch {
 val job1 = launch {
 fetchMovies()
 }
 val job2 = launch {
 displayLoadingText()
 }
 }
 }
 override fun onCleared() {
 scope.cancel()
 }
}
```

Using the cancel function from the coroutine scope makes it easier to cancel multiple jobs launched
with the specified scope. However, the coroutine scope won’t be able to launch new coroutines after
you called the cancel function on it. If you want to cancel the scope’s coroutines but still want to
create coroutines from the scope later, you can use scope.coroutineContext.cancelChildren() instead:

```kt
class MovieViewModel: ViewModel() { 
 private val scope = CoroutineScope(Dispatchers.Main +
 Job()) 
 init { 
 scope.launch() { 
 val job1 = launch { 
 fetchMovies() 
 } 
 val job2 = launch { 
 displayLoadingText()
 } 
 } 
 } 
 fun cancelAll() { 
 scope.coroutineContext.cancelChildren()
 }
 ...
}
```

Calling the cancelAll function will cancel all the child jobs in the coroutine context of the scope.
You will still be able to use the scope later to create coroutines.
Canceling a coroutine will throw CancellationException, a special exception that indicates the
coroutine was canceled. This exception will not crash the application.

When you cancel a coroutine, the coroutine’s job’s state will change to Cancelling.
It won’t automatically go to the Cancelled state and cancel the coroutine. The coroutine
can continue to run even after the cancelation, unless your coroutine has code that
can stop it from running.

##### Managing coroutine timeouts

When your application is doing a background task, you may want to stop it because it is taking too
long. You can manually track the time and cancel the task. Or you can use the withTimeoutsuspending
function. With the withTimeout function, you can set your timeout in milliseconds orDuration. Once
this timeout is exceeded, it will throw **TimeOutCancellationException**, a subclass of
CancellationException

```kt
class MovieViewModel: ViewModel() {
 init {
 viewModelScope.launch {
 val job = launch {
 withTimeout(5_000L) {
 fetchMovies()
 }
 }
 ...
 }
 }
}
```

A timeout of 5,000 milliseconds (5 seconds) has been set for the coroutine. If the fetchMovies task
takes longer than that, the coroutine will time out and throw TimeoutCancellationException.

Another function you can use is withTimeoutOrNull. It is similar to the withTimeout
function, but it will return null if the timeout was exceeded. Here’s an example of how you can use
withTimeoutOrNull.

```kt
class MovieViewModel: ViewModel() {
 init {
 viewModelScope.launch() {
 val job = async {
 fetchMovies()
 }
 val movies = withTimeoutOrNull(5_000L) {
 job.await()
 }
 ...
 }
 }
 ...
}
```

The coroutine will return null if fetchMovies times out after 5 seconds, and if not, it will return
the list of movies fetched.

##### Catching exceptions in coroutines

To handle exceptions in your coroutines, you can simply use try-catch. For example, if you have a
coroutine started with a launch coroutine builder, you can do the following to handle exceptions

```kt
 init {
 viewModelScope.launch() {
 try {
 fetchMovies()
 } catch (exception: Exception) {
 Log.e("MovieViewModel",
 exception.message.toString())
 }
 }
 }
}

```

If your coroutine was built using the async coroutine builder, the exception will be thrown when you
call the await functi

```kt
class MovieViewModel: ViewModel() {
 init {
 viewModelScope.launch() {
 val job = async {
 fetchMovies()
 }
 var movies = emptyList<Movie>()
 try {
 movies = job.await()
 } catch (exception: Exception) {
 Log.e("MovieViewModel",
 exception.message.toString())
 }
 }
 }
}


```

When a coroutine encounters an exception, it will cancel the job and pass on the exception to its
parent. This parent coroutine will be canceled, as well as its children. Exceptions in the child
coroutines will not affect the parent and its sibling coroutines if you use SupervisorJob as
follows:
• Creating the coroutine scope with the suspending supervisorScope{} builder
• Using SupervisorJob for your coroutine scope: CoroutineScope(SupervisorJob())

When handling coroutine exceptions, you can also use a single place to handle these exceptions with
CoroutineExceptionHandler. CoroutineExceptionHandler is a coroutine context element that you can add
to your coroutine to handle uncaught exceptions

```kt
class MovieViewModel: ViewModel() {
 private val exceptionHandler =
 CoroutineExceptionHandler { _, exception ->
 Log.e("MovieViewModel",
 exception.message.toString())
 }
 
 private val scope = CoroutineScope(exceptionHandler)

}

```
