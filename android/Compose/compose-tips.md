we can use @Composable as a part of input function 

```kt
@Composable
fun AnimatingFabContent(
    icon: @Composable () -> Unit,
    text: @Composable () -> Unit,
    modifier: Modifier = Modifier,
    extended: Boolean = true
)
```

---

### Layout :

In Compose, UI elements are represented by the composable functions that emit a piece of UI when invoked, that is then added to a UI tree that gets rendered on the screen. Each UI element has one parent and potentially many children. Each element is also located within its parent, specified as an (x, y) position, and a size, specified as a `width` and a `height`.

Parents define the constraints for their child elements. An element is asked to define its size within those constraints

Laying out each node in the UI tree is a three step process. Each node must:

1. Measure any children
2. Decide its own size
3. Place its children

Measuring a layout can only be done during the measurement and layout passes, and a child can only be placed during the layout passes.

```kt
fun Modifier.customLayoutModifier() =
    layout { measurable, constraints ->
        // ...
    }
```

The `layout` modifier only changes the calling composable.

```kt
Text("Hi there!", Modifier.firstBaselineToTop(32.dp))
```

 To measure and layout multiple composables, use the `Layout` composable instead. This composable allows you to measure and lay out children manually. All higher-level layouts like `Column` and `Row` are built with the `Layout` composable.

**note**:  In the View system, creating a custom layout required extending `ViewGroup` and implementing measure and layout functions. In Compose you simply write a function using the `Layout` composable.

Layout Example 

```kt
@Composable
fun MyBasicColumn(
    modifier: Modifier = Modifier,
    content: @Composable () -> Unit
) {
    Layout(
        modifier = modifier,
        content = content
    ) { measurables, constraints ->
        // measure and position children given constraints logic here
        // ...
    }
}
```

---

## then

we can **then** on modifier to attach 2 modifier together 

for example

```kt
    val backgroundModifier = if (selected) {
        Modifier.background(
            color = LocalContentColor.current,
            shape = RoundedCornerShape(14.dp)
        )
    } else {
        Modifier
    }
```

usage :

```kt
 IconButton(
        onClick = onClick,
        modifier = modifier.then(backgroundModifier)
    )
```

 or we can use like this :

```kt
paddingSizeModifier.then(Modifier.clip(CircleShape))
```

---

## CenterAlignAppBar

it comes from material 3 

the title will be centered  when we use centerAlignAppBar because of 

<u>centeredTitle = true,</u>

in this AppBar  we have one parameter  and that name is 

```kt
scrollBehavior: TopAppBarScrollBehavior? = null
```

and for behavior ,we use this item 

```kt
  val topBarState = rememberTopAppBarState()

  val scrollBehavior = TopAppBarDefaults.pinnedScrollBehavior(topBarState)
```

TopAppBarDefaults = Contains default values used for the top app bar implementations.

pinnedScrollBehavior = Returns a pinned TopAppBarScrollBehavior that tracks nested-scroll callbacks and updates its TopAppBarState.contentOffset accordingly.

action   = this used for CenterAppBar  and its not and action 
actully it is an composable scope we can define an icon 

according to the document definition =
**the actions displayed at the end of the top app bar. This should typically be IconButtons. The default layout here is a Row, so icons inside will be placed horizontally**

----

#### @DrawableRes

@DrawableRes is an annotation that can be used **to indicate that an integer parameter, field or method return value is expected to be a drawable resource reference** (e.g. R. drawable. my_image ). This annotation is used by the Android build tools to validate that the resource reference is valid at compile time.

----

## Semantics :



Compose uses *semantics properties* to pass information to accessibility services. Semantics properties provide information about UI elements that are displayed to the user. Most built-in composables like [`Text`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary#Text(kotlin.String,androidx.compose.ui.Modifier,androidx.compose.ui.graphics.Color,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.font.FontStyle,androidx.compose.ui.text.font.FontWeight,androidx.compose.ui.text.font.FontFamily,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.style.TextDecoration,androidx.compose.ui.text.style.TextAlign,androidx.compose.ui.unit.TextUnit,androidx.compose.ui.text.style.TextOverflow,kotlin.Boolean,kotlin.Int,kotlin.Function1,androidx.compose.ui.text.TextStyle)) and [`Button`](https://developer.android.com/reference/kotlin/androidx/compose/material/package-summary#Button(kotlin.Function0,androidx.compose.ui.Modifier,kotlin.Boolean,androidx.compose.foundation.interaction.MutableInteractionSource,androidx.compose.material.ButtonElevation,androidx.compose.ui.graphics.Shape,androidx.compose.foundation.BorderStroke,androidx.compose.material.ButtonColors,androidx.compose.foundation.layout.PaddingValues,kotlin.Function1)) fill these semantics properties with information inferred from the composable and its children. Some modifiers like [`toggleable`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/selection/package-summary#toggleable(androidx.compose.ui.Modifier,kotlin.Boolean,kotlin.Boolean,androidx.compose.ui.semantics.Role,kotlin.Function1)) and [`clickable`](https://developer.android.com/reference/kotlin/androidx/compose/foundation/package-summary#clickable(androidx.compose.ui.Modifier,kotlin.Boolean,kotlin.String,androidx.compose.ui.semantics.Role,kotlin.Function0)) will also set certain semantics properties. However, sometimes the framework needs more information to understand how to describe a UI element to the user.



A Composition [describes the UI](https://developer.android.com/jetpack/compose/mental-model) of your app and is produced by running composables. The Composition is a tree-structure that consists of the composables that describe your UI.

Next to the Composition, there exists a parallel tree, called the *Semantics tree*. This tree describes your UI in an alternative manner that is understandable for [Accessibility](https://developer.android.com/jetpack/compose/accessibility) services and for the [Testing](https://developer.android.com/jetpack/compose/testing) framework. Accessibility services use the tree to describe the app to users with a specific need. The Testing framework uses it to interact with your app and make assertions about it. The Semantics tree does not contain the information on how to *draw* your composables, but it contains information about the **semantic *meaning*** of your composables.

---

key point of lifecycle and remember :
The `remember`ed stuff only lasts till the Composable gets destroyed. If you navigate to another screen, the previous Composables are destroyed and along with them, the scroll state

---













---

https://medium.com/androiddevelopers/fundamentals-of-compose-layouts-and-modifiers-64d794664b66

https://medium.com/androiddevelopers/thinking-in-compose-c4ef150bb7cf
