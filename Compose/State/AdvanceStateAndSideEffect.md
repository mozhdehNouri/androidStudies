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

In this example, `**SideEffect**` creates a mutable state object using `**mutableStateOf**`, with an initial value of an empty string. Now on button click we are updating the text and on text update we want to update the value of `**i**`. But `**Button**` composable can recompose even without the click which will not change the text but will increment value of `**i**`**.** If it was a network call then it would make a network call on every recomposition of `**Button**`**.**

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

# DisposableEffect :





















Resource :

https://medium.com/ymedialabs-innovation/jetpack-compose-essentials-side-effects-8f25a74de96c

https://proandroiddev.com/mastering-side-effects-in-jetpack-compose-b7ee46162c01
