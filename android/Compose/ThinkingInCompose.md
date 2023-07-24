### Thinking in compose

as you know the compose approach is different from fragment and XML 



##### xml approche

Android view hierarchy has been representable as a tree of UI widgets. As the state of the app changes because of things like user interactions, the UI hierarchy needs to be updated to display the current data. The most common way of updating the UI is to walk the tree using functions like [`findViewById()`](https://developer.android.com/reference/android/view/View#findViewById(int)), and change nodes by calling methods like `button.setText(String)`, `container.addChild(View)`, or `img.setImageBitmap(Bitmap)`. These methods change the internal state of the widget.



Compose Approch :

compose is a declarative ui framework you describe what your ui should contain, and compose handles the rest.

this way of writing ui is very different from views and you have to shift your thinking to build apps with compose.



with views, you describe step by step how to get your ui to look a certain way. you do this by defining xml , finding views from xml  in code , and then calling setter function to get your ui too look the way you want.

with compose , you no longer have to write xml. ui can entierly be described in code in kotlin.

Constructing UI by describing  what , not how. the key difference between compose and views.



image in we have an item of recyclerView and it has image, text and radio button horizontally 


in Views, these elements would all be define in xml. and we have to refrence each view in kotlin or java in our fragment or activity using findViewById.

after obtaining refrences we would then mutate each view by calling setter function like setText or setImage to display  ui state.

when we have a manually update views when state changes is error prone. it's possible to forget to update a view with its dependent state.

Synchronizing state changes throughout the lifespan of an app is a recurring challenge when working with views. this problem also increases in complexity as the number of  views and dependent state grow in your app. it solvable problem but it's a common source of bugs.



in compose  : instead of writing this is xml, we would define our elements directly  in code , in kotlin doing it this way we no longer have to jump between XML and code as the UI would already be declared in code.

in compose  ui elementa are function and not object. its mean you can't find  a refrence to them.

instead UI elements are entirely controll by the state or argument that you pass.

here we are just describing what our ui should look like. no calls to findViewById, setImage or setText.

we are declaring what our ui should look like by providing the necessary states where our UI functions. but we are not telling compose how  it should render that state.

so if state controls the ui, how do we go about updating state to update the UI?

in compose we do that through events. when the user interacts with the UI element the UI emits an event such as on click. and the event handler can then decide if the ui state should be changed.

if the state changes, the functions, or UI elements that depend in that state will re-executed and we call it recomposition.



the process of converting state into Ui and state changes causing UI to regenerate is at the core of how compose works as ui as a ui framework.









##### more performance tips for jetpack compose:

1- always ensure you are testing in release mode with R8 enable.

2- write a benchmark











[More performance tips for Jetpack Compose - YouTube](https://www.youtube.com/watch?v=ahXLwg2JYpc)
