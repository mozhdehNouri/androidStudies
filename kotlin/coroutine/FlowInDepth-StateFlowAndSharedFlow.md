## SharedFlow and StateFlow

StateFlow and SharedFlow Don’t be fooled by the naming, even though they contain`Flow` in their name, they are unlike the original flows, hot streams

let's start with an example :

**when using flows in UI you should pay attention about changing the configuration:**

For example, screen rotation or an update may cause a configuration change. In such use cases, we should be able to protect the current data with a buffer and share it with other collectors regardless of which lifecycle it is in. We can achieve this by using `StateFlow`. Even if there are no collectors, it stores the data. It’s safe to utilize with activities or fragments because you can collect multiple times from it. You could use the mutable version of stateflow to change the value at any time but that isn’t very responsive. Instead, any flow can be converted to a stateflow. The stateflow receives all updates from the upstream flows and saves the most recent value.

consider this example of learning better StateFlow consept :

```kt
class MainViewModel: ViewModel() {
    private val repository = MessageRepository()

    val messages = repository.fetchMessages().stateIn(
        initialValue = "Initial Value",
        scope = viewModelScope,
        started = WhileSubscribed(5000)
    )
}
```

explain :

You can use the `stateIn()` operator to convert a flow to a stateflow.  
It has three parameters: `initialValue` since a stateflow must always have a value, `scope` which controls when the sharing begins, and `started` which takes the value of how much time is required before upstream flows are killed.

##### definition of StateFlow and SharedFlow :

Both the StateFlow and SharedFlow are Hot Flows.

##### StateFlow :

StateFlow makes sure, if **`(x == y)`** the do nothing but if **`(x !=y)`** then only emit the new value i.e. **y** in this case.

```kt
val stateFlow = MutableStateFlow(0)
```

starting collect :

```kt
stateFlow.collect {
    println(it)
}
```

output :

```kt
0
```

We get this "0" as it takes an initial value and emits it immediately.

And then, if we keep on setting the values as below:

```kt
stateFlow.value = 1
stateFlow.value = 2
stateFlow.value = 2
stateFlow.value = 1
stateFlow.value = 3
```

output :

```kt
1
2
1
3
```

Notice here that we are getting "2" only once, not twice. As it does not emit consecutive repeated values.

Suppose we add a new collector now:

```kt
stateFlow.collect {
    println(it)
}
```

output :

```cmake
3
```

As the StateFlow stores the last value and emits it as soon as a new collector starts collecting.

SharedFlow :

```kt
val sharedFlow = MutableSharedFlow<Int>()
```

when call collect :

```kt
sharedFlow.collect {
    println(it)
}
```

As soon as we start collecting, we will not get anything as it does not take an initial value.

And then, if we keep on emitting the values as below:

```kt
sharedFlow.emit(1)
sharedFlow.emit(2)
sharedFlow.emit(2)
sharedFlow.emit(1)
sharedFlow.emit(3)
```

output :

```kt
1
2
2
1
3
```

Notice here that we are getting "2" twice. As it emits consecutive repeated values also.

Suppose we add a new collector now:

```kt
sharedFlow.collect {
    println(it)
}
```

We will not get anything as the SharedFlow does not store the last value.

**Now, that we have seen the examples of both of them. We can understand the below points.**

StateFlow is a type of SharedFlow. StateFlow is a specialization of SharedFlow.

StateFlow is a SharedFlow with a fixed replay = 1 with some more additions. That means new collectors will immediately get the current state as soon as they start collecting.

In a simple way, we can say using the pseudo-code:

```kt
StateFlow = SharedFlow
            .withInitialValue(initialValue)
            .replay(count=1)
            .distinctUntilChanged()
```

In fact, we can get the StateFlow behavior using SharedFlow as below:

```kotlin
val sharedFlow = MutableSharedFlow<Int>(
    replay = 1,
    onBufferOverflow = BufferOverflow.DROP_OLDEST
)
sharedFlow.emit(0) // initial value
val stateFlow = sharedFlow.distinctUntilChanged()
```

an example of both :

```kt
// Constructor takes no value
val mySharedFlow = MutableSharedFlow<Int>()
// Constructor takes a value
val myStateFlow = MutableStateFlow<Int>(0)
...
mySharedFlow.emit(1)
myStateFlow.emit(1)
```

**where we can use StateFlow and ShareFlow in our Android Project:**

UseCase : Get the list of the users from the network and show them in the UI.

```kt
val usersStateFlow = MutableStateFlow<UiState<List<User>>>(UiState.Loading)
```

```kkt
usersStateFlow.collect {

}
```

Now, as soon as we open the activity, the Activity will subscribe to collect. The following will be collected:

usersStateFlow: loading state as StateFlow takes the initial value and emits it immediately.

Now, when our viewModel fetches the data from the network. It will set the data to the usersStateFlow.

```kt
usersStateFlow.value = UiState.Success(usersFromNetwork)
```

Our Activity collector will get the data of the users and show them in the UI.

Now, if **orientation changes**, the ViewModel gets retained, and our collector present in the Activity will resubscribe to collect. The following will be collected:

usersStateFlow: List of users which was set from the network. (StateFlow keeps the last value).

**Advantage: No need for a new network call.**

Now, let's try to use the SharedFlow in place of the StateFlow.

We have a SharedFlow in our ViewModel.

```kotlin
val usersSharedFlow = MutableSharedFlow<UiState<List<User>>>()
```

And we have a collector in our Activity.

```kotlin
usersSharedFlow.collect {

}
```

Now, as soon as we open the activity, the Activity will subscribe to collect. Nothing will get collected here as SharedFlow is used.

Now, when our viewModel fetches the data from the network. It will set the data to the usersSharedFlow.

```kotlin
usersSharedFlow.emit(UiState.Success(usersFromNetwork))
```

Our Activity collector will get the data of the users and show them in the UI.

Now, if **orientation changes**, the ViewModel gets retained, and our collector present in the Activity will resubscribe to collect. Nothing will get collected here as SharedFlow is used which does not store any data. We will have to make a new network call.

**Disadvantage: Unnecessary network call as we were already having the data.**

So, in this case, we should use StateFlow instead of SharedFlow. However, we can modify SharedFlow to store data as we have seen above about getting the StateFlow behavior using the SharedFlow.

So, in this case, we should use StateFlow instead of SharedFlow. However, we can modify SharedFlow to store data as we have seen above about getting the StateFlow behavior using the SharedFlow.

Now, we will take another example to learn where to use SharedFlow instead of StateFlow.

Suppose, suppose we are doing a task, if that task gets failed, we have to show Snackbar.

We have a SharedFlow in our ViewModel.

```kotlin
val showSnackbarSharedFlow = MutableSharedFlow<Boolean>()
```

And we have a collector in our Activity.

```kotlin
showSnackbarSharedFlow.collect {

}
```

Now, as soon as we open the activity, the Activity will subscribe to collect. Nothing will get collected here as SharedFlow is used.

Then, when our viewModel starts the task and gets failed. It will set the value true to showSnackbarSharedFlow.

```kotlin
showSnackbarSharedFlow.emit(true)
```

Our Activity collector will get the value as true and show the Snackbar.

Now, if **orientation changes**, the ViewModel gets retained, and our collector present in the Activity will resubscribe to collect. Nothing will get collected here as SharedFlow does not keep the last value. And that is fine. We should not show the Snackbar again on orientation changes.

**Advantage: It does not show Snackbar again as intended.**

Now, let's try to use the StateFlow in place of the SharedFlow.

We have a StateFlow in our ViewModel.

```kotlin
val showSnackbarStateFlow = MutableStateFlow(false)
```

And we have a collector in our Activity.

```kotlin
showSnackbarStateFlow.collect {

}
```

Now, as soon as we open the activity, the Activity will subscribe to collect. The following will get collected.

showSnackbarStateFlow: false as StateFlow takes the initial value and emits it immediately.

Now, when our viewModel starts the task and gets failed. It will set the value true to showSnackbarSharedFlow.

```kotlin
showSnackbarStateFlow.value = true
```

Our Activity collector will get the value as true and show the Snackbar.

Now, if **orientation changes**, the ViewModel gets retained, and our collector present in the Activity will resubscribe to collect. The following will be collected:

showSnackbarStateFlow: true will get collected here as StateFlow keeps the last value. It will show the Snackbar again. And that is not fine. We should not show the Snackbar again on orientation changes.

**Disadvantage: It shows Snackbar again which is not required.**

So, in this case, we should use SharedFlow instead of StateFlow.

## StateFlow vs SharedFlow :

the main difference between a SharedFlow and a StateFlow is that a StateFlow takes a default value through the constructor and emits it immediately when someone starts collecting, while a SharedFlow takes no value and emits nothing by default.

this forces you to pay attention your data is a state (use StateFlow then) or an event (use SharedFlow). a state could be the visibility of your UI component and it always has a value (shown/hidden) while an event can only be triggered if and only if one or multiple preconditions are fulfilled.

| StateFlow                                                                                                                                                                                                              | SharedFlow                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| Hot Flow                                                                                                                                                                                                               | Hot Flow                                                                                                                        |
| Needs an initial value and emits it as soon as the collector starts collecting.                                                                                                                                        | Does not need an initial value so does not emit any value by default.                                                           |
| `val stateFlow = MutableStateFlow(0)`                                                                                                                                                                                  | `val sharedFlow = MutableSharedFlow<Int>()`                                                                                     |
| Only emits the last known value.                                                                                                                                                                                       | Can be configured to emit many previous values using the replay operator.                                                       |
| It has the value property, we can check the current value. It keeps a history of one value that we can get directly without collecting.                                                                                | It does not have the value property.                                                                                            |
| It does not emit consecutive repeated values. It emits the value only when it is distinct from the previous item.                                                                                                      | It emits all the values and does not care about the distinct from the previous item. It emits consecutive repeated values also. |
| Similar to LiveData except for the Lifecycle awareness of the Android component. We should use repeatOnLifecycle scope with StateFlow to add the Lifecycle awareness to it, then it will become exactly like LiveData. | Not similar to LiveData.                                                                                                        |

##### StateFlow vs LiveData

### StateFlow, Flow, and LiveData

`StateFlow` and [`LiveData`](https://developer.android.com/topic/libraries/architecture/livedata) have similarities. Both are observable data holder classes, and both follow a similar pattern when used in your app architecture.

Note, however, that `StateFlow` and [`LiveData`](https://developer.android.com/topic/libraries/architecture/livedata) do behave differently:

- `StateFlow` requires an initial state to be passed in to the constructor, while `LiveData` does not.
- `LiveData.observe()` automatically unregisters the consumer when the view goes to the `STOPPED` state, whereas collecting from a `StateFlow` or any other flow does not stop collecting automatically. To achieve the same behavior,you need to collect the flow from a `Lifecycle.repeatOnLifecycle` block.

#### Making cold flows hot using <u>shareIn</u> :

`StateFlow` is a *hot* flow—it remains in memory as long as the flow is collected or while any other references to it exist from a garbage collection root. You can turn cold flows hot by using the  `shareIn `operator.

```kt
class NewsRemoteDataSource(...,
    private val externalScope: CoroutineScope,
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        ...
    }.shareIn(
        externalScope,
        replay = 1,
        started = SharingStarted.WhileSubscribed()
    )
}
```

#### Combining Flow operator :

combining two flows into one. There are a few ways to do this. 

##### Merge :

merging the elements from two flows into one. No modifications are made, no matter from which flow elements originate

```kt
import kotlinx.coroutines.flow.*

suspend fun main() {
    val ints: Flow<Int> = flowOf(1, 2, 3)
    val doubles: Flow<Double> = flowOf(0.1, 0.2, 0.3)

    val together: Flow<Number> = merge(ints, doubles)
    print(together.toList())
    // [1, 0.1, 0.2, 0.3, 2, 3]
    // or [1, 0.1, 0.2, 0.3, 2, 3]
    // or [0.1, 1, 2, 3, 0.2, 0.3]
    // or any other combination
}
```

It is important to know that when we use `merge` the elements from one flow do not wait for another flow.For instance, in the example below, elements from the first flow are delayed, but this does not stop the elements from the second flow.

```kt
import kotlinx.coroutines.delay
import kotlinx.coroutines.flow.*

suspend fun main() {
    val ints: Flow<Int> = flowOf(1, 2, 3)
        .onEach { delay(1000) }
    val doubles: Flow<Double> = flowOf(0.1, 0.2, 0.3)

    val together: Flow<Number> = merge(ints, doubles)
    together.collect { println(it) }
}
// 0.1
// 0.2
// 0.3
// (1 sec)
// 1
// (1 sec)
// 2
// (1 sec)
// 3
```

##### Zip :

zip makes pairs from both flows. We also need to specify a function that decides how elements are paired (transformed into one what will be emitted in the new flow). Each element can only be part of one pair, so it needs to wait for its pair. Elements left without a pair are lost, so when the zipping of a flow is complete, the resulting flow is also complete (as is the other flow). Each element from one flow is paired with a corresponding element from the other flow, according to the order in which they are emitted. Once an element is paired, it cannot be paired with any other element, ensuring that each element is included in only one pair.



```kt

```

















question of my self :

if stateFlow is hot why we call collect?

and what reasone of stateFlow and sharedFlow is hot ? why them not cold?

---

Resource: 

https://medium.com/yemeksepeti-teknoloji/introduction-to-kotlin-flows-827f5a71ad7e#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6Ijg2OTY5YWVjMzdhNzc4MGYxODgwNzg3NzU5M2JiYmY4Y2Y1ZGU1Y2UiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJuYmYiOjE2ODE5MjQ5MjMsImF1ZCI6IjIxNjI5NjAzNTgzNC1rMWs2cWUwNjBzMnRwMmEyamFtNGxqZGNtczAwc3R0Zy5hcHBzLmdvb2dsZXVzZXJjb250ZW50LmNvbSIsInN1YiI6IjExODM3NDYzMDAwNDcxODUyMzA3NiIsImVtYWlsIjoibW9qZGVub3VyaTc3QGdtYWlsLmNvbSIsImVtYWlsX3ZlcmlmaWVkIjp0cnVlLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJuYW1lIjoibW96aGRlaCBub3VyaSIsInBpY3R1cmUiOiJodHRwczovL2xoMy5nb29nbGV1c2VyY29udGVudC5jb20vYS9BR05teXhhYXJqYnNVS2hKU3kzVjNoWlZCZV9XdWltWWFseHMxLUpkQWtEOT1zOTYtYyIsImdpdmVuX25hbWUiOiJtb3poZGVoIiwiZmFtaWx5X25hbWUiOiJub3VyaSIsImlhdCI6MTY4MTkyNTIyMywiZXhwIjoxNjgxOTI4ODIzLCJqdGkiOiI3ZDdkYTdjYmNiOGZkN2QwZDNlNDU4ZDg5MjM4MTg0NGYxOWVkYzVkIn0.cEv0GHE_Ro0QGdaRKDjeg0Sh95ZjA4LhXShJGr9e_5bB3p8w6YO_LalkXYuxwSV8-vODukWz5zfMwoycpbsKrwDIrUU2W5zdNDU202f8QeFgAC-HiK2LR7y2Kk0BHJpUmztonlkd-e939O1eqXWmHWM46KBIMtaipG_VGCIm_PmNt6Isiz3cEtWtoglDniFqb4I7L__jg38AnurMKz-fWhcPr2-4cyHIcwuLsf8S6MMG4teD4EP-9E3vwx4QWny2GOlVoKs-OLyXlRWtUc05KBg0n_mrEdDMIpjuEpymf-g5OMuV1m0uh9Mbz2wcfnV0_jM2pvArcyO60sGevdasKg

[StateFlow and SharedFlow](https://amitshekhar.me/blog/stateflow-and-sharedflow)

https://medium.com/swlh/introduction-to-flow-channel-and-shared-stateflow-e1c28c5bc755#:~:text=As%20you%20see%2C%20the%20main,and%20emits%20nothing%20by%20default.

https://blog.mindorks.com/stateflow-apis-in-kotlin/#:~:text=Note%3A%20A%20regular%20Flow%20is%20cold%20but%20StateFlow%20is%20hot.
