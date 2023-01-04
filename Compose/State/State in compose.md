# State in compose

- Working with Jetpack Compose in Android and want to update the UI with newly updated data . This can be handled by using Jetpack Compose’s state management. 

- An app's "state" is any value that can change over time

### In Jetpack Compose, what is a state:

- a state is an object that contains data that is mapped to one or more widgets.

- We can update the data shown in the widgets by using the value from the state object.

- The value of the state can change during runtime, which allows us to update the widget with new data.

- The composable in Jetpack Compose updates itself based on the value of the state.

- When the value is changed, the composable function only re-composes the composable whose data has been changed and ignores the others.

- When we recompose the UI, the UI tree does not redraw itself but only updates the specific composable because redrawing the entire UI would be a very expensive task. 

#### Compose states can be managed in a variety of ways :

### how does it determPine which compostables to update ?

Jetpack Compose is based on the concept of Positional Memoization, which is a technique for optimizing program function calls and returning the cached result. When we draw the UI for the first time in Compose, it caches all the composable in the UI tree

###### **Compose states can be managed in a variety of ways**

We can manage the state in Jetpack Compose in two ways.

1. **MutableState** – In this case, the state stores the value on execution and, if any composable is subscribed to it, the composable updates the value if it changes.
2. **Model** – We use Model as an annotation to any class that will assist us in updating the UI.

#### StateHoisting :

###### first of all you have to konw about state ful and state less meaning in jetpack compose:

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

State Hoisting :













## State tips:

1- the composable in Jetpack Compose are subscribed to a state, and when the value of the state is updated, the value of all the composable who are subscribed to it is also updated. All three texts are subscribed to a state in this case, and when the value of state changes, the text in these three is also updated.

2- State determines what is shown in the UI at any particular time





refrence :

https://iandamping.medium.com/understanding-stateful-stateless-in-jetpack-compose-b64daf6b00a5

https://developer.android.com/jetpack/compose/state#stateful-vs-stateless
