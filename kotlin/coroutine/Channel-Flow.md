### Coroutine Flow :

**befor deep dive into flow you should konw briefly about channel in coroutine:**

a channel is a way for two coroutines to communicate with each other. It provides a mechanism for sending and receiving data between coroutines, allowing them to coordinate and synchronize their actions.

A channel can be thought of as a pipeline between two coroutines, where one coroutine puts data into the pipeline and the other coroutine takes data out of the pipeline. The data can be of any type, including custom classes or data structures.

Overall, channels in Kotlin Coroutine provide a powerful and flexible mechanism for communication and synchronization between coroutines, making it easier to write efficient, high-performance concurrent code.

##### example :

```kt
fun main() = runBlocking {
    val channel = Channel<Int>()

    // producer coroutine
    launch {
        for (i in 1..5) {
            println("Producing $i")
            channel.send(i)
        }
        channel.close()
    }

    // consumer coroutine
    launch {
        for (i in channel) {
            println("Consuming $i")
        }
    }
}
```

output :

```kt

```

**explain :** In this example, we create a channel of integers and launch two coroutines. one to produce integers and send them to the channel, and one to consume integers from the channel.

The producer coroutine uses a `for` loop to produce the integers 1 to 5, sending each integer to the channel using the `send` method. After sending all the integers, it calls the `close` method on the channel to indicate that no more data will be sent.

The consumer coroutine uses a `for` loop to consume integers from the channel, printing each integer to the console using `println`. 

## Flow :

flow is a Kotlin language feature that can emit multiple values sequentially over some time. For example, you can use a flow to fetch the latest updates from an API and modify them with flow operators.

##### what is coldStream and hotStream?

Hot streams transmit data even if there is no subscriber when the data arrives. In cold streams, the incoming data is transmitted only if there is a subscriber. 

For example, let’s say you are fetching data from an API. You can do this by using `channels`. Channels are an example of hot stream. there will always be data flow in hot streams, it is necessary to keep the subscribers under control Because it can cause data losses (if the subscriber is forgotten) or even memory leaks (open network connection etc). 

On the other hand, cold streams start pushing values only when you start collecting. Kotlin flow is an implementation of cold stream, powered by Kotlin Coroutines.  when you cancel the scope, you also release any running coroutines so the same rules apply to Kotlin flow as well. When you cancel the scope, you also dispose of the flow. 

## **Flow Builders:**

 **several different flow builders for creating flows.**

1. with `flow{}` builder, you create a flow from the suspendable lambda block. Inside the block, you are able to do many operations.

```kt
fun createFlow() = flow {
    (1..5).forEach {
        emit(it)
    }

}

fun main() = runBlocking { createFlow().collect { println(it) } }
```

2. you can use `flowOf()` to create a Flow from a fixed set of values.

```kt
fun createFlow() = flowOf(1,2,3,4,5)

fun main() = runBlocking { createFlow().collect { println(it) } }
```

3. you can convert various collections and sequences to a Flow using `asFlow()` builder.

```kt
fun createFlow() = listOf(1,2,3,4,5).asFlow()

fun main() = runBlocking { createFlow().collect { println(it) } }
```

## **Flow Operators:**

There are two types of operators inside flow:  ***intermediate*** and ***terminal***.  

Intermediate operators are used to modifying the data flow between the producer and the consumer.

```kt
fun requestNumbers() = flow {
    (1..5).forEach {
        emit(it)
        delay(100)
    }
}

fun toUiModel(number: Int) = "Model $number"

fun main() = runBlocking { 
    requestNumbers()
        .filter { number -> number > 2 }
        .map { number -> toUiModel(number) }
        .catch { error -> println(error) }
        .collect { println(it) } 
}
```

**explain :** 

-  `filter()` operator to get the numbers we need

- `map()` operator to transform the data to uiModel which is a better abstraction for this layer of the app. 

- `catch()` operator catches exceptions that could happen while processing items in the upstream flow.

Collecting flows usually happen from the UI layer as it is where we want to display the data on the screen.

 We need to use a `terminal operator` (collect, reduce, launchIn etc.) to start listening for values. To get all the values in the stream as they are emitted, we use the `collect()` operator. 

When you apply a terminal operator to a flow, the flow is created on-demand and starts emitting values. 

Every time `collect()` is called, a new flow or pipe will be created and its producer block starts refreshing from the API at its own interval. We refer to this type of flows as cold flows as they are created on-demand and emit data only when they are being observed.

```kt
fun requestNumbers() = (1..5).asFlow().transform { number ->
    log("Sending $number")
    emit("Model $number")
}

fun main() = runBlocking { 
    requestNumbers()
        .take(2)
        .collect { model -> 
            println(model) 
            log("Collecting $model")
        } 
}
```

**look at this example :**

```kt
val numbers = flowOf(1,1,1,2,2,3,4,4,4,4,5,5,6,6,7,7,8,9,9,10)

fun main() = runBlocking { 
    numbers
        .onStart { println("onStart") }
        .distinctUntilChanged()
        .takeWhile { it != 6 }
        .onEach { println(it) }
        .onCompletion { println("onCompletion") }
        .collect()
}
```

We return a repeating list from 1 to 10 as flow.

- the `onStart()` is where it is entered before the flow is collected

- `onCompletion()` is the place entered when the flow is completed or canceled.

- Each time a new value arrives, it is entered into `onEach()`.

- `distinctUntilChanged()` we filter out all subsequent occurrences of the same values

- `takeWhile()` we pass the values to the next operator until the given condition is met. click [here](https://flowmarbles.com/)

- the Flow starts emitting data when `collect()` is called on the stream

```kt
myIntFlow.collect{ intValue -> /* intValue = 1 */ }
```

#### zip and combine oprator

The `zip()` and `combine()` operators combine the corresponding values of the two streams.

```kt
val numbers = (1..3).asFlow().onEach { delay(300) }
val numberTexts = flowOf("one", "two", "three").onEach { delay(400) }

fun main() = runBlocking { 
    var startTime = System.currentTimeMillis()
    numbers.zip(numberTexts) { number, text -> "$number = $text"}
        .collect { value -> 
            println("$value at ${System.currentTimeMillis() - startTime} ms from start") 
        } 
    println("*********************")
    startTime = System.currentTimeMillis()
    numbers.combine(numberTexts) { number, text -> "$number = $text"}
        .collect { value -> 
            println("$value at ${System.currentTimeMillis() - startTime} ms from start") 
        }
}
```

**explain :**

`numbers` variable is updated every 300ms while `numberTexts` variable is updated every 400ms. Compressing them using the `zip()` operator will still produce the same result, even if the results are printed every 400ms but `combine()` merges the most recently emitted values by each flow.

output :

```kt
1 = one at 443 ms from start
2 = two at 843 ms from start
3 = three at 1245 ms from start
*********************
1 = one at 407 ms from start
2 = one at 609 ms from start
2 = two at 808 ms from start
3 = two at 910 ms from start
3 = three at 1208 ms from start
```

-----

#### Safe way to collecting flow in ui

when the flow isn’t visible on the screen, the UI should stop collecting data from it.

There are several options for doing so, all of which are aware of the UI lifecycle. Live data or lifecycle coroutine specific APIs like `repeatOnLifecycle()` and `flowWithLifecycle()` can be used.

`Lifecycle.repeatOnLifecycle`, and `Flow.flowWithLifecycle` APIs protect you from wasting resources and why they’re a good default to use for flow collection in the UI layer.

Wasting Resource :

It’s [recommended](https://developer.android.com/kotlin/coroutines/coroutines-best-practices#coroutines-data-layer) to expose the `Flow<T>` API from lower layers of your app hierarchy regardless of the flow producer implementation details. However, you should also collect them safely.

A cold flow backed by a `channel` or using operators with buffers such as 

`buffer` , `conflate` , `flowOn` ,`shareIn` is **not safe to collect** with some of the existing APIs such as  `CoroutineScope.launch` , `Flow<T>.launchIn`, or `LifecycleCoroutineScope.launchWhenX` , unless you manually cancel the `Job` that started the coroutine when the activity goes to the background. These APIs will keep the underlying flow producer active while emitting items into the buffer in the background, and thus wasting resources.

**Note: Internally, `callbackFlow` uses a channel, which is conceptually very similar to a blocking queue, and has a default capacity of 64 elements.**

**note : `asLiveData ` can trun flow into a liveData and liveData is lifecycle awar.**

```kt
   val messages = repository.fetchMessages().asLiveData() 

   ///usage in ui layer
     viewModel.messages.observe(this) { message ->
            binding.mainTextView.text = message
        }
```

##### Lifecycle.repeatOnLifecycle :

`repeatOnLifecycle()` is recommended way to collect flows from the UI layer

It is a suspend function that takes `Lifecycle.State` as a parameter and also it is lifecycle aware so it automatically launches a new coroutine with the block passed to it when that lifecycle reaches a specific given state.

Something important to notice is that the coroutine that calls `repeatOnLifecycle()` won’t resume executing until the lifecycle is destroyed. If you need to collect numerous flows, use `launch` inside the `repeatOnLifecycle()` block to generate multiple coroutines.

```kt
class LocationActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Create a new coroutine since repeatOnLifecycle is a suspend function
        lifecycleScope.launch {
            // The block passed to repeatOnLifecycle is executed when the lifecycle
            // is at least STARTED and is cancelled when the lifecycle is STOPPED.
            // It automatically restarts the block when the lifecycle is STARTED again.
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                // Safely collect from locationFlow when the lifecycle is STARTED
                // and stops collection when the lifecycle is STOPPED
                locationProvider.locationFlow().collect {
                    // New location! Update the map
                }
            }
        }
    }
}
```

```kt
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                viewModel.messages.collect { message ->
                    binding.mainTextView.text = message
                }
            }
```

**note :** this avoids any boilerplate code since the associated code to cancel the coroutine when it’s no longer needed is automatically done by `repeatOnLifecycle`. As you could guess, it’s recommended to call this API in the activity’s `onCreate` or fragment’s `onViewCreated` methods to avoid unexpected behaviors.

**note :** Fragments should *always* use the `viewLifecycleOwner` to trigger UI updates. However, that’s not the case for `DialogFragment`s which might not have a `View` sometimes. For `DialogFragment`s, you can use the `lifecycleOwner`.

```kt
class LocationFragment: Fragment() {
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        // ...
        viewLifecycleOwner.lifecycleScope.launch {
            viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
                locationProvider.locationFlow().collect {
                    // New location! Update the map
                }
            }
        }
    }
}
```

#### Flow.flowWithLifecycle

use the `Flow.flowWithLifecycle` operator when you have only one flow to collect.

This API uses the `repeatOnLifecycle` API under the hood.

and emits items and cancels the underlying producer when the `Lifecycle` moves in and out of the target state.

```kt
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        lifecycleScope.launch {
            viewModel.messages
                .flowWithLifecycle(lifecycle, Lifecycle.State.STARTED)
                .collect { message ->
                    binding.mainTextView.text = message
                }
```

```kt
class LocationActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Listen to one flow in a lifecycle-aware manner using flowWithLifecycle
        lifecycleScope.launch {
            locationProvider.locationFlow()
                .flowWithLifecycle(lifecycle, Lifecycle.State.STARTED)
                .collect {
                    // New location! Update the map
                }
        }

        // Listen to multiple flows
        lifecycleScope.launch {
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                // As collect is a suspend function, if you want to collect
                // multiple flows in parallel, you need to do so in 
                // different coroutines
                launch {
                    flow1.collect { /* Do something */ }   
                }

                launch {
                    flow2.collect { /* Do something */ }
                }
            }
        }
    }
}
```

#### lifecycle.launchWhenStarted

```kt
class LocationActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Collects from the flow when the View is at least STARTED and
        // SUSPENDS the collection when the lifecycle is STOPPED.
        // Collecting the flow cancels when the View is DESTROYED.
        lifecycleScope.launchWhenStarted {
            locationProvider.locationFlow().collect {
                // New location! Update the map
            } 
        }
        // Same issue with:
        // - lifecycleScope.launch { /* Collect from locationFlow() here */ }
        // - locationProvider.locationFlow().onEach { /* ... */ }.launchIn(lifecycleScope)
    }
}
```

**note**: above example keeps the flow emitting locations even if the view is not displaying them in the UI`lifecycleScope.launchWhenStarted` suspends the execution of the coroutine. New locations are not processed, but the `callbackFlow` producer keeps sending locations nonetheless. Using the `lifecycleScope.launch` or `launchIn` APIs are even more dangerous as the view keeps consuming locations even if it’s in the background! Which could potentially make your app crash.

when you using `lifecycleScope.launch` or `launchIn` you should cancel manually collection

```kt
class LocationActivity : AppCompatActivity() {

    // Coroutine listening for Locations
    private var locationUpdatesJob: Job? = null

    override fun onStart() {
        super.onStart()
        locationUpdatesJob = lifecycleScope.launch {
            locationProvider.locationFlow().collect {
                // New location! Update the map
            } 
        }
    }

    override fun onStop() {
        // Stop collecting when the View goes to the background
        locationUpdatesJob?.cancel()
        super.onStop()
    }
}
```

exp: that’s boilerplate

note : and finally we have reached the end of the article  i  guess  question has been created for you and that is it what is difference between channels and flow? and why i explain about channel ?

Both can be used for streams, a flow emits where a channel can receive and emit data. The main difference between flows and channels is this: **Flows are *usually* cold** and **channels are hot**. In other words, when using a flow the data is produced within the stream while in **channels** the data is produced outside of the stream.



###### Diffrence between launchWhenX and repeatOnLifeCycle ?

launchWhenStarted suspends the execution of the coroutine, and repeatOnLifecycle cancels and restarts a new coroutine This is the difference



and why use repeatOnLifecycle is bestPractice launchWhenX is not ?

`repeatOnLifecycle` restarts its coroutine from scratch on each repeat, and cancels it each time lifecycle falls below the specified state. It’s a natural fit for collecting most flows, because it fully cancels the flow when it’s not needed, which saves resources related to the flow continuing to emit values.

`launchWhenX` doesn’t cancel the coroutine and restart it. It just postpones when it starts, and pauses execution while below the specified state. If you use `launchWhenX` to start a coroutine, you will need to manually cancel the coroutine when the lifecycle state changes, or risk potential issues like memory leaks.



--------



- why sharedFlow and stateFlow are hot stream and flow is Cold ?

- the learning what is that mening of oprator like- take -zip -combine- transform-

- what is upStream and downStream

- what is conection between channel and flow.

https://developer.android.com/kotlin/flow#:~:text=In%20coroutines%2C%20a%20flow%20is,and%20can%20provide%20multiple%20values.

https://developer.android.com/codelabs/advanced-kotlin-coroutines#0

https://medium.com/yemeksepeti-teknoloji/introduction-to-kotlin-flows-827f5a71ad7e

https://medium.com/androiddevelopers/a-safer-way-to-collect-flows-from-android-uis-23080b1f8bda

https://medium.com/swlh/introduction-to-flow-channel-and-shared-stateflow-e1c28c5bc755#:~:text=As%20you%20see%2C%20the%20main,and%20emits%20nothing%20by%20default.

[android - Difference between launchWhenStarted and repeatOnLifecycle(STARTED) in collecting flows - Stack Overflow](https://stackoverflow.com/questions/71113597/difference-between-launchwhenstarted-and-repeatonlifecyclestarted-in-collectin)
