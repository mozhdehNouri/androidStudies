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







































https://medium.com/androiddevelopers/fundamentals-of-compose-layouts-and-modifiers-64d794664b66

https://medium.com/androiddevelopers/thinking-in-compose-c4ef150bb7cf
