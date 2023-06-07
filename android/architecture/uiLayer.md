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





























https://developer.android.com/topic/architecture/ui-layer#state-holders

https://medium.com/kayvan-kaseb/the-implementation-of-ui-layer-in-android-app-architecture-6efc8f24a16f
