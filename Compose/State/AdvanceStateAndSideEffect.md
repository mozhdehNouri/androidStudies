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

`**LaunchedEffect**` is a composable function that is used to launch a coroutine inside the scope of composable, when `**LaunchedEffect**` enters the composition, it launches a coroutine and cancels when it leaves composition. `**LaunchedEffect**` takes multiple keys as params and if any of the key changes it cancels the existing coroutine and launch again. This is useful for performing side effects, such as making network calls or updating a database, without blocking the UI thread.



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











Resource :

https://medium.com/ymedialabs-innovation/jetpack-compose-essentials-side-effects-8f25a74de96c

https://proandroiddev.com/mastering-side-effects-in-jetpack-compose-b7ee46162c01
