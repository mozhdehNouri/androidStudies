#### Compose Lifecycle :

When Jetpack Compose runs your composables for the first time, during *initial composition*, it will keep track of the composables that you call to describe your UI in a Composition. Then, when the state of your app changes, Jetpack Compose schedules a *recomposition*. Recomposition is when Jetpack Compose re-executes the composables that may have changed in response to state changes, and then updates the Composition to reflect any changes.



A Composition can only be produced by an initial composition and updated by recomposition. The only way to modify a Composition is through recomposition.



**Key Point:** The lifecycle of a composable is defined by the following events: entering the Composition, getting recomposed 0 or more times, and leaving the Composition.



**Note:** A Composable's' lifecycle is simpler than the lifecycle of views, activities, and fragments. When a composable needs to manage or interact with external resources that *do* have a more complex lifecycle, you should use [effects](https://developer.android.com/jetpack/compose/lifecycle#state-effect-use-cases).



If a composable is called multiple times, multiple instances are placed in the Composition. Each call has its own lifecycle in the Composition.



```kt

```
https://developer.android.com/codelabs/jetpack-compose-state#0