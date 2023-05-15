## Lifecycle overview

 a Composition **describes the UI of your app and is produced by running composables**

Think of a Composition as a tree-like structure that represents your UI. At the top of the tree, you have the root composable, which is the starting point of your UI. From there, you have child composables that represent different components or elements of your UI, such as buttons, text fields, or images.

Each composable function you write contributes to building this tree-like structure. Composables are like building blocks that you assemble together to create your app's UI. The Composition is the result of running these composables, meaning that they are executed to create the actual UI.

By running the composables, Compose constructs the Composition, which is a complete representation of your app's UI hierarchy. This allows Compose to efficiently handle updates and changes to your UI by recomposing only the necessary parts of the Composition when needed.

In summary, a Composition in Compose is a tree-like structure that describes your app's UI. It's created by running composables, which are individual building blocks that define different parts of the UI. The Composition is the result of assembling these composables together and serves as the blueprint for how your app's UI should look and behave.

When Jetpack Compose runs your composables for the first time, during *initial composition*, it will keep track of the composables that you call to describe your UI in a Composition. Then, when the state of your app changes, Jetpack Compose schedules a *recomposition*. Recomposition is when Jetpack Compose re-executes the composables that may have changed in response to state changes, and then updates the Composition to reflect any changes.

A Composition can only be produced by an initial composition and updated by recomposition. The only way to modify a Composition is through recomposition.

**Key Point:** The lifecycle of a composable is defined by the following events: entering the Composition, getting recomposed 0 or more times, and leaving the Composition.

Recomposition is typically triggered by a change to a [`State<T>`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/State) object. Compose tracks these and runs all composables in the Composition that read that particular `State<T>`, and any composables that they call that cannot be [skipped](https://developer.android.com/jetpack/compose/lifecycle#skipping).

**Note:** A Composable's' lifecycle is simpler than the lifecycle of views, activities, and fragments. When a composable needs to manage or interact with external resources that *do* have a more complex lifecycle, you should use [effects](https://developer.android.com/jetpack/compose/lifecycle#state-effect-use-cases).

If a composable is called multiple times, multiple instances are placed in the Composition. Each call has its own lifecycle in the Composition.

```kt
@Composable
fun MyComposable() {
    Column {
        Text("Hello")
        Text("World")
    }
}
```

## Anatomy of a composable in Composition

The instance of a composable in Composition is identified by its **call site**. The Compose compiler considers each call site as distinct. Calling composables from multiple call sites will create multiple instances of the composable in Composition.

#### what is that mean of above sentences :

In the context of Android development with Compose, a "composable" refers to a function or a component that can be used to build user interfaces. Composables are reusable and can be called from different parts of your code.

The sentence you mentioned is explaining how Compose handles the instances of composables when they are called from multiple places (or "call sites") in your code.

Let's break it down:

1. "Calling composables from multiple call sites": This means that you are using the same composable function in different parts of your code. For example, you may have a composable function called "MyButton" that creates a custom button, and you call it in different parts of your app to display buttons.

2. "Will create multiple instances of the composable in Composition": Each time you call a composable from a different call site, Compose will create a separate instance of that composable in what's called the Composition. The Composition is essentially the current state of your user interface.

To understand this better, think of the Composition as a container or a tree that holds the different components of your UI. When you call a composable, it gets added to the Composition, creating an instance of it. If you call the same composable from multiple call sites, each call will create a separate instance in the Composition.

This behavior is important because it allows Compose to track the state and changes of each instance of a composable independently. If you were to update one instance, it wouldn't affect the others. This helps maintain the desired behavior and separation between different parts of your UI.

If during a recomposition a composable calls different composables than it did during the previous composition, Compose will **identify which composables were called or not called** and for the composables that were called in both compositions, Compose will **avoid recomposing them if their inputs haven't changed**

#### what is that meaning of above sentences :

1. "If during a recomposition a composable calls different composables than it did during the previous composition": When Compose recomposes (re-evaluates) a composable function, it checks if the composable is calling different composables (other functions) compared to the previous composition.

2. "Compose will identify which composables were called or not called": Compose will keep track of the composables that were called and not called during the current recomposition.

3. "For the composables that were called in both compositions, Compose will avoid recomposing them if their inputs haven't changed": Among the composables that were called in both the current and previous compositions, Compose will check if the inputs (the values passed to the composables) have changed. If the inputs remain the same, Compose will avoid recomposing those composables.

In simpler terms:

When a composable is recomposed, Compose looks at which other composables it calls. It keeps track of which ones were called or not called. If a composable was called in both the current and previous compositions, Compose checks if the inputs to that composable have changed. If the inputs haven't changed, Compose avoids recomposing that composable.

This behavior is beneficial because it optimizes the performance of your app. Compose avoids unnecessary recompositions of composables whose inputs haven't changed, reducing the amount of work needed to update the UI and improving overall efficiency.