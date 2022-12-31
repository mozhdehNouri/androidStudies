# State in compose

Working with Jetpack Compose in Android and want to update the UI with newly updated data . This can be handled by using Jetpack Compose’s state management. 

### In Jetpack Compose, what is a state:

- a state is an object that contains data that is mapped to one or more widgets.

- We can update the data shown in the widgets by using the value from the state object.

- The value of the state can change during runtime, which allows us to update the widget with new data.

- The composable in Jetpack Compose updates itself based on the value of the state.

- When the value is changed, the composable function only re-composes the composable whose data has been changed and ignores the others.

- When we recompose the UI, the UI tree does not redraw itself but only updates the specific composable because redrawing the entire UI would be a very expensive task. 

#### Compose states can be managed in a variety of ways :

### how does it determine which compostables to update ?

Jetpack Compose is based on the concept of Positional Memoization, which is a technique for optimizing program function calls and returning the cached result. When we draw the UI for the first time in Compose, it caches all the composable in the UI tree





###### **Compose states can be managed in a variety of ways**

We can manage the state in Jetpack Compose in two ways.

1. **MutableState** – In this case, the state stores the value on execution and, if any composable is subscribed to it, the composable updates the value if it changes.
2. **Model** – We use Model as an annotation to any class that will assist us in updating the UI.



## State tips:

1- the composable in Jetpack Compose are subscribed to a state, and when the value of the state is updated, the value of all the composable who are subscribed to it is also updated. All three texts are subscribed to a state in this case, and when the value of state changes, the text in these three is also updated.
