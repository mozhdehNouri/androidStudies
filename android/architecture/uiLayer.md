# UI layer

 Google has offered that each Android app should include two layers at least: First, the *UI layer* that demonstrates app data on the screen. Second, the [*data layer*](https://medium.com/p/3277bf31c07f) that includes the business logic of your app and exposes application data.

Nevertheless, to ease and reuse the interactions between the UI and data layers, you can be able to add an optional layer called the [*domain layer*](https://medium.com/kayvan-kaseb/enhancing-android-app-architecture-with-domain-layer-8bf51387d10c), which sits between the UI and data layers.

The role of the UI is to display the application data on the screen and also to serve as the primary point of user interaction.

Whenever the data changes, either due to user interaction (like pressing a button) or external input (like a network response), the UI should update to reflect those changes.

the application data you get from the data layer is usually in a different format than the information you need to display. For example, you might only need part of the data for the UI, or you might need to merge two different data sources to present information that is relevant to the user. Regardless of the logic you apply, you need to pass the UI all the information it needs to render fully. *The UI layer is the pipeline that converts application data changes to a form that the UI can present and then displays it.*

## UI layer architecture

The term *UI* refers to UI elements such as activities and fragments that display the data, independent of what APIs they use to do this

the important concepts about ui layer is :

- How to define the UI state.
- Unidirectional data flow (UDF) as a means of producing and managing the UI state.
- How to expose UI state with observable data types according to UDF principles.
- How to implement UI that consumes the observable UI state.

## Define UI state

In other words: if the UI is what the user sees, the UI state is what the app says they should see

Like two sides of the same coin, the UI is the visual representation of the UI state. Any changes to the UI state are immediately reflected in the UI.

##### UI layer includes two issues: UI Element + UI State = UI

In other words, UI elements render the data on the screen. So, you can be able to create these elements via Views or [Jetpack Compose](https://developer.android.com/jetpack/compose) functions.

As it was mentioned, the main role of the data layer is holding, handling, and providing access to the app data. Thus, the UI layer has to accomplish the following steps successfully:

1. Transforming App data to UI specific app data.

2. Updating UI elements to reflect UI specific data.

3. Processing user input events that lead to UI changes.

4. Repeating all the above steps as long as necessary.

### State holders

The classes that are responsible for the production of UI state and contain the necessary logic for that task are called *state holders*. State holders come in a variety of sizes depending on the scope of the corresponding UI elements that they manage, ranging from a single widget like a [bottom app bar](https://material.io/components/app-bars-bottom) to a whole screen or a navigation destination.

In the latter case, the typical implementation is an instance of a [ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel), although depending on the requirements of the application, a simple class might suffice. The News app from the [case study](https://developer.android.com/topic/architecture/ui-layer#case-study), for example, uses a `NewsViewModel` class as a state holder to produce the UI state for the screen displayed in that section.



##### UDF:

The pattern where the state flows down and the events flow up is called a unidirectional data flow (UDF). The implications of this pattern for app architecture are as follows:

- The ViewModel holds and exposes the state to be consumed by the UI. The UI state is application data transformed by the ViewModel.
- The UI notifies the ViewModel of user events.
- The ViewModel handles the user actions and updates the state.
- The updated state is fed back to the UI to render.
- The above is repeated for any event that causes a mutation of state.

For navigation destinations or screens, the ViewModel works with repositories or use case classes to get data and transform it into the UI state while incorporating the effects of events that may cause mutations of the state.



### Types of logic

- **Business logic** is the implementation of product requirements for app data. Business logic is usually placed in the domain or data layers, but never in the UI layer.
- **UI behavior logic** or **UI logic** is *how* to display state changes on the screen. Examples include obtaining the right text to show on the screen using Android [`Resources`](https://developer.android.com/reference/android/content/res/Resources), navigating to a particular screen when the user clicks a button, or displaying a user message on the screen using a [toast](https://developer.android.com/guide/topics/ui/notifiers/toasts) or a [snackbar](https://developer.android.com/training/snackbar).

The UI logic, particularly when it involves UI types like [`Context`](https://developer.android.com/reference/android/content/Context), should live in the UI, not in the ViewModel. If the UI grows in complexity and you want to delegate the UI logic to another class to favor testability and separation of concerns, **you can create a simple class as a state holder**. Simple classes created in the UI can take Android SDK dependencies because they follow the lifecycle of the UI; ViewModel objects have a longer lifespan.



### Why use UDF?

It also separates the place where state changes originate, the place where they are transformed, and the place where they are finally consumed. This separation lets the UI do exactly what its name implies: display information by observing state changes, and relay user intent by passing those changes on to the ViewModel.

In other words, UDF allows for the following:

- **Data consistency.** There is a single source of truth for the UI.
- **Testability.** The source of state is isolated and therefore testable independent of the UI.
- **Maintainability.** Mutation of state follows a well-defined pattern where mutations are a result of both user events and the sources of data they pull from.



## Expose UI state

After you define your UI state and determine how you will manage the production of that state, the next step is to present the produced state to the UI. Because you're using UDF to manage the production of state, you can consider the produced state to be a stream—in other words, multiple versions of the state will be produced over time. As a result, you should expose the UI state in an observable data holder like `LiveData` or `StateFlow`. The reason for this is so that the UI can react to any changes made in the state without having to manually pull data directly from the ViewModel. These types also have the benefit of always having the latest version of the UI state cached, which is useful for quick state restoration after configuration changes.

```kt
class NewsViewModel(...) : ViewModel() {   
 private val _uiState = MutableStateFlow(NewsUiState())   
 val uiState: StateFlow<NewsUiState> = _uiState.asStateFlow()    ...}
}
```

**Note:** In Jetpack Compose apps, you can use Compose's observable [State APIs](https://developer.android.com/jetpack/compose/state#state-in-composables) such as `mutableStateOf` or `snapshotFlow` for the exposure of UI state. Any type of observable data holder such as `StateFlow` or `LiveData` that you see in this guide can be easily consumed in Compose using the appropriate [extensions](https://developer.android.com/jetpack/compose/libraries#streams).



## Consume UI state

To consume the stream of `UiState` objects in the UI, you use the terminal operator for the observable data type that you're using. For example, for `LiveData` you use the `observe()` method, and for Kotlin flows you use the `collect()` method or its variations.When consuming observable data holders in the UI, make sure you take the lifecycle of the UI into consideration. This is important because the UI shouldn’t be observing the UI state when the view isn’t being displayed to the user.

























https://developer.android.com/topic/architecture/ui-layer#state-holders

https://medium.com/kayvan-kaseb/the-implementation-of-ui-layer-in-android-app-architecture-6efc8f24a16f
