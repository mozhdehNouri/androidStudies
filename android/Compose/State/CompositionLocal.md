For example, once the UI finishes displaying on the screen, there is no way to update it in Compose. Instead, you update the UI state.

But how can a composable know about its state and its changes? This is where unidirectional data flow comes into play. 

Once you set the new state, a *recomposition* — the process that recreates the UI with the new state – takes place.

Once you set the new state, a *recomposition* — the process that recreates the UI with the new state – takes place.









In Android Jetpack Compose, `CompositionLocal` is a way to share values between composables without explicitly passing them as parameters.

- You create a `CompositionLocal` using the `compositionLocalOf` function. This creates a container for a value.

- You can set the value using the `CompositionLocalProvider` composable, which sets the value for a specific composition hierarchy.

- Composables within that hierarchy can access the value using the `Local` prefix followed by the `CompositionLocal` name.

```kt
val CurrentTheme = compositionLocalOf { Theme.Light }

@Composable
fun MyApp() {
    CompositionLocalProvider(CurrentTheme provides Theme.Dark) {
        // Value of CurrentTheme is Dark within this composition hierarchy

        MyComposable()
    }
}

@Composable
fun MyComposable() {
    val currentTheme = LocalCurrentTheme.current

    // Access the currentTheme value here
}
```

In the example, we define a `CompositionLocal` called `CurrentTheme`. By using `CompositionLocalProvider`, we set the value of `CurrentTheme` to `Theme.Dark` within the `MyApp` hierarchy. In the `MyComposable` function, we access the value using `LocalCurrentTheme.current`.

With `CompositionLocal`, you can share values within a composition hierarchy without passing them explicitly, making it convenient for accessing commonly used values in your Jetpack Compose app.



[`CompositionLocal`](https://developer.android.com/reference/kotlin/androidx/compose/runtime/CompositionLocal) is a tool for passing data down through the Composition implicitly.











[CompositionLocal in Jetpack Compose | Kodeco](https://www.kodeco.com/34513206-compositionlocal-in-jetpack-compose)

https://developer.android.com/reference/kotlin/androidx/compose/runtime/CompositionLocal
