define some definition :

- **Composition:** a description of the UI built by Jetpack Compose when it executes composables.
- **Initial composition:** creation of a Composition by running composables the first time.
- **Recomposition:** re-running composables to update the Composition when data changes.

# State in compose

In its most general, "state" refers to any data that is hold in memory (either RAM or on disk)

In other words, something that is "state" has two properties:

Basic description :

1. It exists over time. This is what `remember` does.

2. It can change. This is what `mutableStateOf` does.



- Working with Jetpack Compose in Android and want to update the UI with newly updated data . This can be handled by using Jetpack Compose’s state management.

- An app's "state" is any value that can change over time

- In context of Jetpack Compose, a state is a value that is related to updating your UI ,  Every time a state value changes, Jetpack Compose will automatically update the UI for you or can call recompose

- Type of state can be of anything simple Boolean or string or complex data

- The state is an object that is connected/subscribed to one or more widgets, contains data. or we can say a state is an object that contains data that is mapped to one or more widgets.

- any change happens in data, it will update all its subscribed UI widgets. The values of the state are changed at runtime

- We can update the data shown in the widgets by using the value from the state object.

- The value of the state can change during runtime, which allows us to update the widget with new data.

- The composable in Jetpack Compose updates itself based on the value of the state.

- When the value is changed, the composable function only re-composes the composable whose data has been changed and ignores the others.

- When we recompose the UI, the UI tree does not redraw itself but only updates the specific composable because redrawing the entire UI would be a very expensive task.

- Compose to be aware of state changes, your state values need to be wrapped in a `State`

- The value of the state can be of any type. Sometimes it can be as simple as a `Boolean` or a `String`. Other times the state can be a complex data class that contains values for the entire rendered screen

- You can do that using the `mutableStateOf()`

- // moved another place mutableStateOf will return a `MutableState<T>` object and Compose will keep track of changes and update the UI whenever you modify the value

### how does it determPine which compostables to update ?

Jetpack Compose is based on the concept of `Positional Memoization`, which is a technique for optimizing program function calls and returning the cached result. When we draw the UI for the first time in Compose, it caches all the composable in the UI tree

###### **Compose states can be managed in a variety of ways :**

We can manage the state in Jetpack Compose in two ways.

1. **MutableState** – In this case, the state stores the value on execution and, if any composable is subscribed to it, the composable updates the value if it changes.
2. **Model** – We use Model as an annotation to any class that will assist us in updating the UI.

What exactly MutableState dose ~~~~:

for Compose to be aware of state changes, your state values need to be wrapped in a `State` object. You can do that using the `mutableStateOf()` function. This will return a `MutableState<T>` object and Compose will keep track of changes and update the UI whenever you modify the value.

look at this example :

```kotlin
var enabled by remember { mutableStateOf(true) 
```

**`mutableStateOf(true)`** will produce a `MutableState<Boolean>` object which will hold the value of our state (which is `true`).

### What if I don't use `mutableStateOf()`?

```kotlin
@Composable
fun MyComponent() {
    var enabled by remember { true }

    // ...
    Text("Enabled is ${enabled}")
}
```

This won't work. Even though in our code we are able to modify the `enabled` value, our UI will not be aware of such a change. As a result, your UI will not be updated when you update the boolean.

With Jetpack compose, we can preserve the state of view in major two ways:

- Using **remember{}**, a composable function that can store a single object in memory, or by using **rememberSaveable{}**, a composable function to restore your UI state after an activity or process is recreated (for example it happens when the screen is rotated in the Android application). We can manage a simple state of view within a compose function itself.
- Using **ViewModel**, a state holder to separate business logic from UI component. ViewModel has a longer lifecycle than the composition as they are lifecycle-aware components so ViewModel can preserve its state across configuration changes.

### How to keep state within a `ViewModel`

```kotlin
class HomeScreenViewModel : ViewModel {

    var inputText by mutableStateOf("")
        private set

    fun onTextChanged(text: String) {
        viewModelScope.launch {
            inputText = text
        }
    }
}
```

Note that in this case you do not need (and cannot) to use the `remember {}` function within the `ViewModel`. You cannot use it because `remember {}` is a composable function and composable functions can only be used from other composable functions. You do not need to use it, as we are using the `viewModel {}` function in our caller composable function. This function is responsible for maintaining the same instance of the `ViewModel` among composable recompositions.

#### StateHoisting :

###### first of all you have to konw about state ful and state less meaning in jetpack compose:

A composable function that maintains its state is called stateful. A composable function that does not keep its state is called stateless

jump to details :

## Stateful :

A composable that uses `remember` to store an object creates internal state, making the composable *stateful*

Stateful is when Composable create, holds and modifies its own State

example :

```kotlin
@Composable
fun Greeting() {
    var isExpanded by remember { mutableStateOf(false) }

    Row(
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp)
    ) {
        Text(
            modifier = Modifier.weight(1f),
            text = context.getString(R.string.lorem),
            maxLines = if (!isExpanded) 4 else Int.MAX_VALUE
        )
}
```

`Greeting `is a Stateful because it create, holds and modifies its own State and in this example `isExpanded` is the State

when we can use that :
where a caller doesn't need to control the state and can use it without having to manage the state themselves.

###### negative points:

composables with internal state tend to be less reusable and harder to test.

## Stateless :

Stateless is when Composable does not create, holds and modify its own State

actully A *stateless* composable is a composable that doesn't hold any state. An easy way to achieve stateless is by using `state hoisting`

```kotlin
@Composable
fun HelloScreen() {
    var name by rememberSaveable { mutableStateOf("") }

    HelloContent(name = name, onNameChange = { name = it })
}

@Composable
fun HelloContent(name: String, onNameChange: (String) -> Unit) {
    Column(modifier = Modifier.padding(16.dp)) {
        Text(
            text = "Hello, $name",
            modifier = Modifier.padding(bottom = 8.dp),
            style = MaterialTheme.typography.h5
        )
        OutlinedTextField(
            value = name,
            onValueChange = onNameChange,
            label = { Text("Name") }
        )
    }
}
```

`HelloContent` is an example of Stateless because it does not create, holds and modify its own State. Rather it only receive & hoist the State and this pattern is called State hoisting

## State Hoisting :

- State Hoisting : move a composable’s state outside of the composable and push it further up, making the composable stateless

- another definition :  you hoists its state

- means to remove any persistent state from a composable function. Instead you pass the state via the parameters of the function

###### why we need to use state Hoisting :

easier to test , tend to have few bugs , and open uo more apportunities for reuse

##### To consider this example :

```
var text by remember { mutableStateOf("") }
```

- `by` has nothing to do with Compose. It declares a `delegated property`

- `remember` and `mutableStateOf` are completely independent concepts. They just happen to be used together a lot. Just like how databases and RxJava are completely independent but fit nicely together for certain use cases.

- `remember` keeps a value (any value) consistent across recompositions.

- `mutableStateOf` returns a `MutableState.

- `MutableState` is just a thing that holds a value, where Compose will automatically observe changes to the value. Think `MutableLiveData`, but you don't need to call `observe` yourself.

## remeber : Keeps a value over time

`remember` is a composable function that can be used to cache expensive operations. You can think of it as a cache which is local to your composable.

```kotlin
val state: Int = remember { 1 }
```

mutableState :

important tip about mutableState:

look at this exmaple :

```kotlin
val state: Int = remember { 1 }
```

The `state` in the above code is immutable. If you want to change that state and also update the UI, you can use a `MutableState`. `Compose` will observe any reads/writes the `MutableState` object and trigger a [recomposition](https://developer.android.com/jetpack/compose/state#composition-and-recomposition) to update the UI.

```kotlin
val state: MutableState<Int> = remember { mutableStateOf(1) }

Text(
   modifier = Modifier.clickable { state.value += 1 },
   text = "${state.value}",
 )
```

You can also use property delegates by keyword for more emphasis:

```kotlin
var state: Int by remember { mutableStateOf(1) }
```

look at this example :

```kotlin
@Composable
fun MyComponent() {
    var enabled by mutableStateOf(true)

    // ...
    Text("Enabled is ${enabled}")
}
```

This won't work either. As soon as you change `enabled` to `false`, Compose will correctly refresh your UI when you update your state. It will also execute `mutableStateOf()` again, recreating the state object and rerendering your UI with the `enabled` value being `true`

important note :
If you are creating a state object within a `ViewModel` you do not need to wrap your states in a `remember {}` function, but your `ViewModel` needs to be remembered somehow. This is handled automatically for you via the dedicated `viewModel {}` or `hiltViewModel()` functions.

the end definition of remebering dose:

The [**remember{}**](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#remember(kotlin.Function0)) can be used to store both mutable [and ]()immutable objects. 

A value computed by remember{} is stored in the Composition during **initial composition**, and the stored value is returned during recomposition.

In each **recomposition**, remember() returns the stored value so the composable can use it. Whenever the stored value has to change, you can update it and remember() will store it. The next time a recomposition occurs, remember() will provide the latest value.

### rememberSavable :

Another variant (added in `alpha12`) called `rememberSaveable` which is similar to `remember`, but the stored value can survive process death or configuration changes.

```scss
val state: MutableState<Int> = rememberSaveable { mutableStateOf(1) }
```

#### mutableStateOf: Observable state holder

State is only really useful if it can change over time. We need a way to change it, and we need a way to react when it changes. In that `var text by remember { mutableStateOf("") }` line, a whole bunch of pieces are in play:

## State tips:

1- the composable in Jetpack Compose are subscribed to a state, and when the value of the state is updated, the value of all the composable who are subscribed to it is also updated. All three texts are subscribed to a state in this case, and when the value of state changes, the text in these three is also updated.

2- State determines what is shown in the UI at any particular time

3 - in xml when you calling setText ui was update Compose will automatically update your UI when the state of the UI is updated

refrence :

https://iandamping.medium.com/understanding-stateful-stateless-in-jetpack-compose-b64daf6b00a5

https://developer.android.com/jetpack/compose/state#stateful-vs-stateless

[remember { mutableStateOf() } – A cheat sheet - DEV Community](https://dev.to/zachklipp/remember-mutablestateof-a-cheat-sheet-10ma)

[Everything you need to know about State in Jetpack Compose with examples](https://www.composables.co/blog/state)

https://c1ctech.com/state-and-state-hoisting-in-android-jetpack-compose/