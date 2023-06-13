##### Every thing about UI State



## Types of UI state and UI logic

Below there are definitions for types of UI state and logic that are used throughout this document.

### UI state

[UI state](https://developer.android.com/topic/architecture/ui-layer#define-ui-state) is the property that describes the UI. There are two types of UI state:

- **Screen UI state** is *what* you need to display on the screen. For example, a `NewsUiState` class can contain the news articles and other information needed to render the UI. This state is usually connected with other layers of the hierarchy because it contains app data.
- **UI element state** refers to properties intrinsic to UI elements that influence how they are rendered. A UI element may be shown or hidden and may have a certain font, font size, or font color. In Android Views, the View manages this state itself as it is inherently stateful, exposing methods to modify or query its state. An example of this are the [`get`](https://developer.android.com/reference/android/widget/TextView#getText()) and [`set`](https://developer.android.com/reference/android/widget/TextView#setText(java.lang.CharSequence)) methods of the [`TextView`](https://developer.android.com/reference/android/widget/TextView) class for its text. In Jetpack Compose, the state is external to the composable, and you can even hoist it out of the immediate vicinity of the composable into the calling composable function or a state holder. An example of this is [`ScaffoldState`](https://developer.android.com/reference/kotlin/androidx/compose/material/ScaffoldState) for the [`Scaffold`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary#Scaffold(androidx.compose.ui.Modifier,androidx.compose.material.ScaffoldState,kotlin.Function0,kotlin.Function0,kotlin.Function1,kotlin.Function0,androidx.compose.material.FabPosition,kotlin.Boolean,kotlin.Function1,kotlin.Boolean,androidx.compose.ui.graphics.Shape,androidx.compose.ui.unit.Dp,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,androidx.compose.ui.graphics.Color,kotlin.Function1)) composable.



### Logic

Logic in an application can be either business logic or UI logic:

- **Business logic** is the implementation of product requirements for app data. For example, bookmarking an article in a news reader app when the user taps the button. This logic to save a bookmark to a file or database is usually placed in the domain or data layers. The state holder usually delegates this logic to those layers by calling the methods they expose.
- **UI logic** is related to *how* to display UI state on the screen. For example, obtaining the right search bar hint when the user has selected a category, scrolling to a particular item in a list, or the navigation logic to a particular screen when the user clicks a button.





## UI logic

When [UI logic](https://developer.android.com/topic/architecture/ui-layer#logic-types) needs to read or write state, you should scope the state to the UI, following its lifecycle. To achieve this, you should hoist the state at the correct level in a composable function. Alternatively, you can do so in a [plain state holder class](https://developer.android.com/topic/architecture/ui-layer/stateholders#ui-logic), also scoped to the UI lifecycle.

Below is a description of both solutions and explanation of when to use which.

### Composables as state owner

Having UI logic and UI element state in composables is a good approach if the state and logic is simple. You can leave your state internal to a composable or hoist as required.

### No state hoisting needed

Hoisting state isn't always required. State can be kept internal in a composable when no other composable need to control it. In this snippet, there is a composable that expands and collapses on tap:



```kt
@Composable
fun ChatBubble(
    message: Message
) {
    var showDetails by rememberSaveable { mutableStateOf(false) } // Define the UI element expanded state

    ClickableText(
        text = AnnotatedString(message.content),
        onClick = { showDetails = !showDetails } // Apply simple UI logic
    )

    if (showDetails) {
        Text(message.timestamp)
    }
}
```

The variable `showDetails` is the internal state for this UI element. It's only read and modified in this composable and the logic applied to it is very simple. Hoisting the state in this case therefore wouldn't bring much benefit, so you can leave it internal. Doing so makes this composable the owner and single source of truth of the expanded state



### Hoisting within composables

If you need to share your UI element state with other composables and apply UI logic to it in different places, you can hoist it higher in the UI hierarchy. This also makes your composables more reusable and easier to test.



### ViewModels as state owner

When you hoist UI state in the `ViewModel`, you move it outside of the Composition.



### Hoisting within composables

If you need to share your UI element state with other composables and apply UI logic to it in different places, you can hoist it higher in the UI hierarchy. This also makes your composables more reusable and easier to test.

ViewModels aren't stored as part of the Composition. They're provided by the framework and they're scoped to a [`ViewModelStoreOwner`](https://developer.android.com/reference/androidx/lifecycle/ViewModelStoreOwner) which can be an Activity, Fragment, navigation graph, or destination of a navigation graph



## State in composables

Composable functions can use the [`remember`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#remember(kotlin.Function0)) API to store an object in memory. A value com

puted by `remember` is stored in the Composition during initial composition, and the stored value is returned during recomposition. `remember` can be used to store both mutable and immutable objects.

[`mutableStateOf`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/package-summary#mutableStateOf(kotlin.Any,androidx.compose.runtime.SnapshotMutationPolicy)) creates an observable [`MutableState<T>`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState), which is an observable type integrated with the compose runtime.





























https://developer.android.com/jetpack/compose/state#viewmodel-state

https://developer.android.com/jetpack/compose/state-hoisting#screen-ui
