### MVI  architecture

#### What is architecture?

We can't put all our code in a one file. So we need to spilit responsibilites and how we decide to
spilit responsibilites into what file and what those file are responsible for doing that is specify
our architecture patterns. like MVVM or MVI

MVVM or MVI just slightly diffrent ways of arranging our code and each one sort of serves their own
special purpose.

Architecture in software development is all about designing and organizing the code in a way that
makes it easy to maintain and understand. when you have a lot of code, it's important to split it up
into different files and components so that each part can be responsible for a specific task.

MVI, or Model-View-Intent architecture, is one such pattern. As you said, it focuses on the single
source of truth principle, which means that all data is stored in one central place and updates to
that data are reflected throughout the app.

This helps to keep the code organized and reduces the risk of bugs and inconsistencies. It also
makes it easier to update and maintain the app over time.

Immutable states means that once a state has been updated, it cannot be changed or modified. This
helps to keep the code consistent and predictable, and it reduces the risk of bugs caused by
unexpected state changes.

As for what happens to the old state, that depends on the implementation of the MVI architecture. In
some cases, the old state may be kept in memory so that it can be referenced later, while in other
cases it may be discarded.

the immutable state is provided to all the layers of the app, which allows for a more consistent and
predictable user experience

MVI :

MVI architecture can indeed be combined with other architecture patterns, such as MVP or MVVM,
depending on the specific needs and requirements of the app.

One way to think about it is that MVI provides a framework for managing the state and interactions
within the app, while other patterns like MVP or MVVM focus on how the different components of the
app communicate with each other.

For example, you could use MVI to manage the state of a view model in an MVVM architecture, or you
could use it to manage the interactions between the model and the view in an MVP architecture.

So, while MVI is a standalone architecture pattern, it can also be used in combination with other
patterns to create a more comprehensive and robust application.

the model in MVI architecture is different from the model in other patterns like MVP or MVVM. In
those patterns, the model typically refers to a collection of classes and functions that represent
the data and business logic of the app.

In MVI, on the other hand, the model is more of a functional mechanism that takes the output from
the intent and manipulates it to produce the next state of the app. This state can then be used to
update the UI or perform other actions.

So, while the term "model" is used in both MVI and other patterns, the way it's used and the role it
plays in the architecture can be quite different.

Architecture patterns can be a bit tricky to wrap your head around, especially since there are so
many different approaches and terms used to describe them.

So, while the MVX acronym is sometimes used to refer to a group of architecture patterns that share
certain similarities, it's important to remember that each pattern is still unique in its own way
and has its own specific implementation and purpose.

For example, while MVI, MVP, and MVVM all share the "M" for model, the way the model is used and the
role it plays in each architecture can be quite different.

---

MVI pattern focuses on the single source of truth principle to provide immutable states to other
layers and unidirectional and immutability of states that represent the result of user actions and
configure UI screens.

MVI works on top of the other patterns, such as MVP or MVVM, with state management mechanisms, which
means MVI architecture can bring the Presenter or ViewModel concepts depending on your architectural
design.

- **Intent**:**Intent**is a definition of interfaces and functions that handle user actions (UI
  events, such as button click events). The functions transform UI events into**Model**’s interfaces
  and deliver the result to**Model**for manipulating it. As its name implies, we could say we have
  the intention to perform**Model**functions.

- **Model**: The definition of**Model**in MVI is completely different compared to MVP and MVVM. In
  MVI,**Model**is a functional mechanism that takes the output from**Intent**and manipulates it to
  UI states that could be rendered in**View**. The UI states are immutable and come from business
  logic, which follows[single source of truth](https://en.wikipedia.org/wiki/Single_source_of_truth)
  and[unidirectional data flow](https://developer.android.com/topic/architecture/ui-layer#udf).

- **View**:**View**in MVI has the same responsibilities as MVP and MVVM, which represents the screen
  and user interactions, such as listeners, and does not contain business logic. One of the biggest
  differences in implementation from other patterns is that the MVI ensures unidirectional data
  flow, so**View**renders UI elements depending on UI states that come from the**Model**.

Now we want to talk about mvi architecture

In the view we have some event happen like click button or text change so we tell the viewModel this
event heppend. we can say our view like fragment or activity or screan dosen't konw what happend
when a click event happend. the view just say a button was clicked and the the viewModel is the
thing controll every thing. that is what will decide what to do when a button clicked.

View -> nothing to know about what happend.

Model -> responsible for return data.

/// check it
can we say mvi is reactive programming manually

---

in mvi we can say we have strong sepration of concerns.

keep your maintability of your app to minimon.

---

MVI

include unidirectional data flow , immutable State and the use as a function.

---

model = represents a state model in mvi should be immutable to ensure a unidirectional data flow
between theme and the other layers in your arch.

intent : Intent in mvi represent an action to be performed that is translate to a change of the
state in your app.

mvi focus of single-source-of-truth to provide immutable states to other layers.

mvi works top of the other patterns such as mvvm , mvp with state management mechanisms.

---

unidirectional

immutable

reactiveness

functional -> every thing in mvi should be represent by a function those function are pure

pure : some input should recive some output.

we can use babylon.orbit:orbit -> open source we do not need have a lot of boiler plate code.

---

the key in mvi pattern is adding the user into the structure of the software.

intent : in mvi is more closely thought of as intention , that is what is the intention like tap a
button there is user intents.

----

mvi is basclly mvvm having viewmodel , fragment, repository

imaging you sharing a viewmodel for 5 fragment that senario can get very complex if every screen be
diffrent you can have a lot of livedata inside of that viewModel. Mvi solve this problem.and much
more managable.

organization is the key for mvi arch.

for intent consider android camera intent you fire camera intent and override onActivity result and
wating for result same happend for intent in mvi.

mvi arch work that perinciple. you fire specific intent with sealdClass usuall and you observe the
result of the intent and when you get the result you update your ui.

---

In software architecture, particularly for Android development, there are various patterns used to
organize the code, separate concerns, and make maintainable and scalable applications. When it comes
to MVI, MVVM, and MVP, these are all architectural patterns for structuring your code in a clear and
logical way:

1. **MVI (Model-View-Intent)**: This is a newer pattern that’s gaining popularity in reactive
   programming and Android app development. MVI works with the following concepts:

    - **Model**: Essentially represents the state of the view at any given time. It’s immutable,
      meaning once it’s created, it cannot be changed.
    - **View**: The UI layer which displays the data (Model) and forwards user actions (Intents) to
      the Presenter or ViewModel.
    - **Intent**: Unlike the traditional meaning of Intent in Android, in MVI, the ‘Intent’ refers
      to the intention to perform an action, like a click. Intents are then translated into state
      changes of the Model.

2. **MVVM (Model-View-ViewModel)**: This pattern is similar to MVP but uses ViewModel, which is a
   more abstract way of preparing data for the View. It reacts to changes in the model and presents
   the data for the UI (View), but it doesn’t directly handle user actions like in MVP or MVI.

3. **MVP (Model-View-Presenter)**: Again, there’s a Model which is quite similar to MVVM and MVI’s
   Model in that it represents the data layer, but the Presenter is a middle-man that handles all
   the UI logic, working as a bridge between the View and the Model.

To address your confusion:

- **Regarding the “Model”**: In the context of MVx patterns (where x can be ‘P’ for Presenter or
  ‘VM’ for ViewModel), the Model typically refers to the parts of an application that handle the
  data and business logic. It often includes things like the domain model, data source interactions,
  repositories, and so on.

- **MVI atop MVVM**: You may have encountered some architectural approaches that mix components from
  different patterns. For example, using ViewModels with MVI would mean that you still structure
  your data and state management around the ViewModel as in MVVM, but you would introduce the
  concept of Intents from MVI for state and event handling.

Believing in one article over another isn’t necessarily the point; it’s more about understanding the
principles behind these patterns and then applying the one that best suits the needs of your project
and your personal or team’s preference. MVI’s strong focus on unidirectional and cyclical data flow
can make state management more predictable compared to MVVM and MVP, but it might also come with a
steeper learning curve.

----

mvi is like close system every thing is specify . there is no way something to go wrong .

event get fire and result is returned.

every thing is handled this is very organized. every thing is really nicelly encapsulate. each
fragment is refrence to the specify interface.

----

mvi pattern focus on single source of truth principle to provide immutable state to other layers .

intent : intent is a definition of interface and functions that handle user actions. the intent
functions teransform ui event into model and deliver the result to model for manipulate.

model : model is functional mechansim that take the output from intetn and manipulates it to ui
state that could be render in view.

1- user click the button

2- these sort of actions turn into intent.

3- the intent are passed to the model.

4- and this model job to process these and link up to any network calls or buusiness logic.

/// check thread safe and non-blocking in mvi concept

----

view -> emit ui events so people click on diffrent things these are turnd into intent that are like
these sort of piece of work.

and piece of work is applied on to amodel and once that happens the loop back is that the when
diffrent thingd change and update itself it is a unidirectinal data flow.

for example:

user pulls the refresh on the screen so a pull to refresh event is going to be emitted from the view
and that is going to be transformed into an intent that would be something called the refresh list
intent.

so when model process an intent like refresh that going to do a call to the network we are going to
end the new list and model will get updated.

---
