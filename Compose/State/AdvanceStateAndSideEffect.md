### Side Effect :

A **side-effect** is a change to the state of the app that happens outside the scope of a composable function.

A side-effect is when something is done under the function that affects outside or escapes from the scope of that function, or simply put any change to an object, variable, state or any initialisations during recomposition that affects outside of that function is a side-effect.

look at this example :

```kt
@Composable
fun HomeScreen(touchHandler: TouchHandler, vm: ViewModel) {
    val drawerState = rememberDrawerState(DrawerValue.Closed)
    vm.fetchInfo()
    touchHandler.enabled = drawerState.isOpen
    //passing a state in arguments and updating here, usage of which
    //can lead to unnecessary recomposition. which also breaks the
    //unidirectional flow, not a side effect but relevent.

    mutableButtonState.value = drawerState.isOpen.not()

    //Navigate to option screen on click
    Button(onClick = {
        navigate(NavPaths.Options)
          }//..
    }
```

Now in the example above `vm.fetchInfo` will execute as many times as HomeScreen recomposes, similar will happen with the on Button’s onClick `navigate`, which will open the same screen multiple times, these all are the side-effects which are not required to be re-executed every time on recomposition.

We know that Compose is declarative, it represents a `State` and recomposes (re-execute) when it changes, which is essentially called an `Effect`. It’s smart enough to recompose just the affected area to save recomposition for some elements on the screen when used properly, on the other hand, they can execute multiple times, in parallel, in any order, or can be cancelled. So we need some approach that is aware of this lifecycle and all these effects.

another example :

When building UIs in Android, managing side effects can be one of the biggest challenges developers face. It is a change of state of the app that happens outside the scope of a composable function

```kt
// Side Effect
private var i = 0

@Composable
fun SideEffect() {
    var text by remember {
        mutableStateOF("")
    }
    Column {
        Button(onClick = { text += "@" }) {
            i++
            Text(text)
        }
    }
}
```

In this example, `**SideEffect**` creates a mutable state object using `**mutableStateOf**`, with an initial value of an empty string. Now on button click we are updating the text and on text update we want to update the value of `**i**`. But `**Button**` composable can recompose even without the click which will not change the text but will increment value of `**i**`**.** If it was a network call then it would make a network call on every recomposition of `**Button**`**.







## Restarting effects

Some effects in Compose, like `LaunchedEffect`, `produceState`, or `DisposableEffect`, take a variable number of arguments, keys, that are used to cancel the running effect and start a new one with the new keys.





# LaunchedEffect

`LaunchedEffect` is a composable function that is used to launch a coroutine inside the scope of composable, when `**LaunchedEffect**` enters the composition, it launches a coroutine and cancels when it leaves composition. `**LaunchedEffect**` takes multiple keys as params and if any of the key changes it cancels the existing coroutine and launch again. This is useful for performing side effects, such as making network calls or updating a database, without blocking the UI thread.

```kt
// Launched Effect
private var i = 0

@Composable
fun SideEffect() {
    var text by remember {
        mutableStateOF("")
    }

    LaunchedEffect(key1 = text) {
        i++
    }
    Column {
        Button(onClick = { text += "@" }) {
            Text(text)
        }
    }
}
```

In the example above, each time the text is updated, a new coroutine is launched and the value of `i` is updated accordingly. This function is side-effect-free, since `i` is only incremented when the text value changes.



difinition 2 :

### LaunchedEffect: run suspend functions in the scope of a composable

To call suspend functions safely from inside a composable, use the [`LaunchedEffect`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#LaunchedEffect(kotlin.Any,kotlin.coroutines.SuspendFunction1)) composable. When `LaunchedEffect` enters the Composition, it launches a coroutine with the block of code passed as a parameter. The coroutine will be cancelled if `LaunchedEffect` leaves the composition. If `LaunchedEffect` is recomposed with different keys (see the [Restarting Effects](https://developer.android.com/jetpack/compose/side-effects#restarting-effects) section below), the existing coroutine will be cancelled and the new suspend function will be launched in a new coroutine.

For example, showing a [`Snackbar`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#Snackbar(androidx.compose.material3.SnackbarData,androidx.compose.ui.Modifier,kotlin.Boolean,androidx.compose.ui.graphics.Shape,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color)) in a [`Scaffold`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary#Scaffold(androidx.compose.ui.Modifier,kotlin.Function0,kotlin.Function0,kotlin.Function0,kotlin.Function0,androidx.compose.material3.FabPosition,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.foundation.layout.WindowInsets,kotlin.Function1))) is done with the [`SnackbarHostState.showSnackbar`](https://developer.android.com/reference/kotlin/androidx/compose/material3/SnackbarHostState) function, which is a suspend function.



```kt
@Composable
fun MyScreen(
    state: UiState<List<Movie>>,
    snackbarHostState: SnackbarHostState
) {

    // If the UI state contains an error, show snackbar
    if (state.hasError) {

        // `LaunchedEffect` will cancel and re-launch if
        // `scaffoldState.snackbarHostState` changes
        LaunchedEffect(snackbarHostState) {
            // Show snackbar using a coroutine, when the coroutine is cancelled the
            // snackbar will automatically dismiss. This coroutine will cancel whenever
            // `state.hasError` is false, and only start when `state.hasError` is true
            // (due to the above if-check), or if `scaffoldState.snackbarHostState` changes.
            snackbarHostState.showSnackbar(
                message = "Error message",
                actionLabel = "Retry message"
            )
        }
    }

    Scaffold(
        snackbarHost = {
            SnackbarHost(hostState = snackbarHostState)
        }
    ) { contentPadding ->
        // ...
    }
}
```







# rememberCoroutineScope :

To ensure that `LaunchedEffect` launches on the first composition, use it as is. But if you need manual control over the launch, use `rememberCoroutineScope` instead. It can be use to obtain a composition-aware scope to launch coroutine outside composable. It is a composable function that returns a coroutine scope bound to the point of Composable where its called. The scope will be cancelled when the call leaves the composition.

```kt
@Composable
fun MyComponent() {
    val coroutineScope = rememberCoroutineScope()
    val data = remember { mutableStateOf("") }

    Button(onClick = {
        coroutineScope.launch {
            // Simulate network call
            delay(2000)
            data.value = "Data loaded"
        }
    }) {
        Text("Load data")
    }

    Text(text = data.value)
}
```

Here, `rememberCoroutineScope` is used to create a coroutine scope that is tied to the Composable function's lifecycle. This lets you manage coroutines efficiently and safely by ensuring they are cancelled when the Composable function is removed from the composition. You can use the `launch` function within the scope to easily and safely manage asynchronous operations.



difinition 2 :

### rememberCoroutineScope: obtain a composition-aware scope to launch a coroutine outside a composable

As `LaunchedEffect` is a composable function, it can only be used inside other composable functions. In order to launch a coroutine outside of a composable, but scoped so that it will be automatically canceled once it leaves the composition, use [`rememberCoroutineScope`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#rememberCoroutineScope(kotlin.Function0)). Also use `rememberCoroutineScope` whenever you need to control the lifecycle of one or more coroutines manually, for example, cancelling an animation when a user event happens.

`rememberCoroutineScope` is a composable function that returns a `CoroutineScope` bound to the point of the Composition where it's called. The scope will be cancelled when the call leaves the Composition.

```kt
@Composable
fun MoviesScreen(snackbarHostState: SnackbarHostState) {

    // Creates a CoroutineScope bound to the MoviesScreen's lifecycle
    val scope = rememberCoroutineScope()

    Scaffold(
        snackbarHost = {
            SnackbarHost(hostState = snackbarHostState)
        }
    ) { contentPadding ->
        Column(Modifier.padding(contentPadding)) {
            Button(
                onClick = {
                    // Create a new coroutine in the event handler to show a snackbar
                    scope.launch {
                        snackbarHostState.showSnackbar("Something happened!")
                    }
                }
            ) {
                Text("Press me")
            }
        }
    }
```









###### so what is diffrent between LaunchEffect and remeberCoroutineScope ?

`launchEffect` and `rememberCoroutineScope` are both used for managing coroutines, but they serve different purposes.

1. `launchEffect`: This is a composable function that allows you to launch a coroutine in response to a certain event or state change. When the event or state changes, the coroutine is executed and performs the specified task asynchronously. It is typically used for performing side effects such as fetching data from a network or performing a long-running computation. `launchEffect` is designed to be used directly within a composable function, allowing you to seamlessly integrate coroutines into your UI logic.

2. `rememberCoroutineScope`: This is a function that provides a coroutine scope that can be remembered across recompositions of a composable function. It is used to ensure that coroutines launched within the scope are canceled appropriately when the composable is no longer active or recomposed. It's especially useful when dealing with coroutines that have a longer lifespan, such as handling user interactions or managing the lifecycle of a screen. By using `rememberCoroutineScope`, you can ensure that the coroutines are properly managed and canceled when needed.

In summary, `launchEffect` is used to launch a coroutine within a composable function, typically for performing side effects, while `rememberCoroutineScope` is used to create a remembered coroutine scope that ensures proper lifecycle management for longer-lived coroutines.

# rememberUpdatedState :

When you want to reference a value in effect that shouldn’t restart if the value changes then use `rememberUpdatedState`. LaunchedEffect restart when one of the value of the key parameter get updated but sometimes we want to capture the changed value inside the effect without restarting it. This process is helpful if we have long running option that is expensive to restart.

```kt
@Composable
fun ParentComponent() {
     setContent {
            ComposeTheme {
                // A surface container using the 'background' color from the theme
                Surface(modifier = Modifier.fillMaxSize(), color = MaterialTheme.colors.background) {
                    var dynamicData by remember {
                        mutableStateOf("")
                    }
                    LaunchedEffect(Unit) {
                        delay(3000L)
                        dynamicData = "New Text"
                    }
                    MyComponent(title = dynamicData)
                }
            }
        }
}

@Composable
fun MyComponent(title: String) {
  var data by remember { mutableStateOf("") }

    val updatedData by rememberUpdatedState(title)

    LaunchedEffect(Unit) {
        delay(5000L)
        data = updatedData
    }

    Text(text = data)
}
```

difinition 2 :



### rememberUpdatedState: reference a value in an effect that shouldn't restart if the value changes

`LaunchedEffect` restarts when one of the key parameters changes. However, in some situations you might want to capture a value in your effect that, if it changes, you do not want the effect to restart. In order to do this, it is required to use `rememberUpdatedState` to create a reference to this value which can be captured and updated. This approach is helpful for effects that contain long-lived operations that may be expensive or prohibitive to recreate and restart.

For example, suppose your app has a `LandingScreen` that disappears after some time. Even if `LandingScreen` is recomposed, the effect that waits for some time and notifies that the time passed shouldn't be restarted:



```kt
@Composable
fun LandingScreen(onTimeout: () -> Unit) {

    // This will always refer to the latest onTimeout function that
    // LandingScreen was recomposed with
    val currentOnTimeout by rememberUpdatedState(onTimeout)

    // Create an effect that matches the lifecycle of LandingScreen.
    // If LandingScreen recomposes, the delay shouldn't start again.
    LaunchedEffect(true) {
        delay(SplashWaitTimeMillis)
        currentOnTimeout()
    }

    /* Landing screen content */
}
```





# DisposableEffect :

Similar to `LaunchedEffect(key, block)` , `DisposableEffect(**key**, **effect**)` also launches on the first composition and on the `**key**` changes, but the difference is that it takes an `**effect**` instead, unlike in `LaunchedEffect` it is not a suspend block but provides a callback `onDispose()` , so once DisposableEffect leaves the composition or the `**key**` changes `onDispose()` will be invoked. This comes in handy when we need to clear something or unregister some listeners etc.



difinition 2 :

### DisposableEffect: effects that require cleanup :

For side effects that need to be *cleaned up* after the keys change or if the composable leaves the Composition, use [`DisposableEffect`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#DisposableEffect(kotlin.Any,kotlin.Function1)). If the `DisposableEffect` keys change, the composable needs to *dispose* (do the cleanup for) its current effect, and reset by calling the effect again.

```kt
@Composable
fun HomeScreen(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStart: () -> Unit, // Send the 'started' analytics event
    onStop: () -> Unit // Send the 'stopped' analytics event
) {
    // Safely update the current lambdas when a new one is provided
    val currentOnStart by rememberUpdatedState(onStart)
    val currentOnStop by rememberUpdatedState(onStop)

    // If `lifecycleOwner` changes, dispose and reset the effect
    DisposableEffect(lifecycleOwner) {
        // Create an observer that triggers our remembered callbacks
        // for sending analytics events
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_START) {
                currentOnStart()
            } else if (event == Lifecycle.Event.ON_STOP) {
                currentOnStop()
            }
        }

        // Add the observer to the lifecycle
        lifecycleOwner.lifecycle.addObserver(observer)

        // When the effect leaves the Composition, remove the observer
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }

    /* Home screen content */
}

```

In the code above, the effect will add the `observer` to the `lifecycleOwner`. If `lifecycleOwner` changes, the effect is disposed and restarted with the new `lifecycleOwner`.

A `DisposableEffect` must include an `onDispose` clause as the final statement in its block of code. Otherwise, the IDE displays a build-time error.



# SideEffect:

`SideEffect` is used to publish compose state to non-compose code. The `SideEffect` is triggered on every recomposition and it is not a coroutine scope, so suspend functions cannot be used within it.



To share Compose state with objects not managed by compose, use the [`SideEffect`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#SideEffect(kotlin.Function0)) composable, as it's invoked on every successful recomposition.



```kt
@Composable
fun rememberFirebaseAnalytics(user: User): FirebaseAnalytics {
    val analytics: FirebaseAnalytics = remember {
        FirebaseAnalytics()
    }

    // On every successful composition, update FirebaseAnalytics with
    // the userType from the current User, ensuring that future analytics
    // events have this metadata attached
    SideEffect {
        analytics.setUserProperty("userType", user.userType)
    }
    return analytics
}

```



### derivedStateOf: convert one or multiple state objects into another state

`derivedStateOf` is a function that allows you to compute a value based on other state values and automatically update that value when the dependencies change.

`derivedStateOf` is a composable that can be used to derive new state based on the values of other state variables. It is useful when you need to compute a value that depends on other values, and you want to avoid recomputing the value unnecessarily.



```kt
@Composable
fun MyComponent() {
    var firstName by remember { mutableStateOf("") }
    var lastName by remember { mutableStateOf("") }

    val fullName = derivedStateOf {
        "$firstName $lastName"
    }

    Text(text = "Full Name: $fullName")
}
```

In the example, we declare a Composable function called `**MyComponent**` that has two mutable state variables called `**firstName**` and `**lastName**`. We then use `**derivedStateOf**` to create a new state variable called `**fullName**` , which concatenates the values of `**firstName**` and `**lastName**`. Whenever `**firstName**` or `**lastName**` changes, `**derivedStateOf**` recomposes the composable and updates `**fullName**`. Finally, we use the `**Text**` composable to display `**fullName**`. `**derivedStateOf**` is useful for computing derived state variables that depend on other state variables, without recomputing the derived state unnecessarily.

```kt
@Composable
fun TodoList(highPriorityKeywords: List<String> = listOf("Review", "Unblock", "Compose")) {

    val todoTasks = remember { mutableStateListOf<String>() }

    // Calculate high priority tasks only when the todoTasks or highPriorityKeywords
    // change, not on every recomposition
    val highPriorityTasks by remember(highPriorityKeywords) {
        derivedStateOf {
            todoTasks.filter { task ->
                highPriorityKeywords.any { keyword ->
                    task.contains(keyword)
                }
            }
        }
    }

    Box(Modifier.fillMaxSize()) {
        LazyColumn {
            items(highPriorityTasks) { /* ... */ }
            items(todoTasks) { /* ... */ }
        }
        /* Rest of the UI where users can add elements to the list */
    }
}

```

### snapshotFlow: convert Compose's State into Flows

`**snapshotFlow**` is a function that allows you to create a flow that emits the current value of a state object, and then emits any subsequent changes to that object. This can be useful for creating reactive UIs that respond to changes in state, without having to manually manage callbacks or listeners.



Use [`snapshotFlow`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#snapshotFlow(kotlin.Function0)) to convert [`State<T>`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State) objects into a cold Flow.







Resource :

https://medium.com/ymedialabs-innovation/jetpack-compose-essentials-side-effects-8f25a74de96c

https://proandroiddev.com/mastering-side-effects-in-jetpack-compose-b7ee46162c01



[Side-effects in Compose &nbsp;|&nbsp; Jetpack Compose &nbsp;|&nbsp; Android Developers](https://developer.android.com/jetpack/compose/side-effects)
