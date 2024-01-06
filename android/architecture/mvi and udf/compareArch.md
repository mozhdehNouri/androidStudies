person #1

use basic mvvm pattern and borrow some concepts from mvi even redux

the idea that the viewmodel should kind of hold state for our views and should be the only source of
truth for that state and when that state is received on the view end.

that view should bind that state and that state should be kind of single source of truth for it.

- have ui state

- and ui have a lot of event so view should send some events to viewmodel and viewmodel will be kind
  of store where you it would receive the events and just mutate that existing state into a new one.

when you have a lot of intraction in specific screen where mvi makes sense .

the arch is depending on project and requirements.

---

person #2

if you are writing a screen that only reads data and displays it mvvm is good enough

if you have a screen that has a lot of moving parts you might want something migt a little more
complex you need something like mvi to more having controll on state managment.

viewmodel is getting data from data layer and convert it to view state which render by view. and
your view should take data from viewmodel and render it to screen.

in mvvm one of problem to consider is that as our dependencies on the viewmodel
viewmodel can be handling a lot of business logic.
it is going to have a lot of things going on state managements going to be harder because of all of
these changes. there is a lot room for error. if we want to more clear state managent is come with
something like mvi.

mvi : in mvi like same we have a view and viewmodel that represent viewstate
but now viewmodel is little diffrent. it dosen't iteract with any dependencies like repository , it
only has one dependencies which is our preferences store. a store is a state management container so
this is our source of truth for what our state of the screen is going to be and it exposes that
state for the viewmodel which will then pass it back to the view effectively.
so all of this is kind of like a proxy between the view and our state management container and what
it passes through are things called actions. our action could be like fetch data when created or can
be a button click it could then conver that to an action and say like submit data.

preferencesStore has dependecy to preferencesReducer that is something called the reducer to change
our state.

action comes into store that action is sent off to the reducer with the
current state and then using
those two pieces , the current state and the action.

the reducer spits out a new state and then that state gets updated and
passed back.

for example the reducer could take in a current state which is like
loading state and then a new
action like preferences loaded and then it will output a new state saying
show the date here are the
preferences to show.

// watch again this movie the boy you do not like it.



---

persion #3

##### why mvi ?

Model = state

view = view

intent = intention

in mvi arch model is not data layer it is state

user interacts with intent and intent change the state and state update
the ui and ui show to the user.

State :

single source of truth



----

person #4

prons

if we will gather all state in one place it will be single source of
truth.

- single source of truth

- easy to check at any particular time.

- clear sequence of changes

- easy to find the cause of the change.

- easy decoupled testing.

hurt of mvi is UDF we send event from view and get state from data.

---

gabor varadi.

why not Mvi ?

1. **Complexity and Boilerplate**: He believes MVI can lead to
   unnecessarily complex code. For simple tasks, there is a significant
   amount of boilerplate code needed, which could be done with much less
   effort.

2. **State Management Overhead**: With MVI, the whole state of the
   application has to be updated even for minor changes. This can be
   inefficient because it might require processing every single previous
   state, which isn’t always necessary.

3. **Android-Specific Issues**: Varadi points out that MVI was adapted
   from web development practices that don’t always fit the Android
   environment, such as assumptions about saving and restoring state that
   don’t hold true for Android’s lifecycle.

4. **Imperative Nature of State Reducers**: In MVI, state reducers create
   new states from the old ones. On Android, this can lead to performance
   issues, as all states must be evaluated sequentially, making quick
   state changes (like typing in a search bar) cumbersome.

5. **Rigid Implementation**: Varadi suggests that many MVI libraries are
   too rigid, overly depending on a single state stream and inherited base
   classes, which tightly couples the architecture to specific frameworks.

He advocates for reactive programming approaches that are more flexible
and fit the naturally asynchronous operations of modern Android apps.
These methods allow for easier management of state without the need to
follow the strict, sequential update process which MVI enforces. In
essence, he is looking for solutions that align better with the inherent
reactivity and asynchronicity of Android app development

Yes, the summary provided reflects Gabor Varadi’s perspective on MVI (
Model-View-Intent) based on his discussions in the article content you
provided earlier. The points of critique, such as increased complexity,
inefficiency of state reducers, the mismatch of web-based patterns with
Android specifics, and the rigid nature of many MVI implementations, align
with Varadi’s arguments. He questions the practicality of MVI in Android
development and suggests that a more reactive and less imperative approach
may be better suited for building modern Android applications.

This aligns with Gabor Varadi’s views on MVI, where he has consistently
advocated for practical, maintainable, and efficient coding practices in
the Android ecosystem. Keep in mind the talks, articles, and discussions
on MVI, and the personal perspectives of developers like Varadi, represent
an ongoing conversation in the community about the best architectural
practices, and opinions may vary among different developers.

the meaning of varadi to say do not use imprative ui state
The phrase “Reclaim the reactivity of your state management, say no to
imperative MVI” essentially means that developers should move towards a
more reactive approach to manage the application state instead of sticking
with the traditional, imperative MVI approach.

Now, let’s break it down:

1. **Reclaim the reactivity**: This refers to adopting a style of
   programming that allows the application to react to changes in data or
   user actions immediately, without needing complex state management
   code. Reactive programming is a paradigm centered around data streams
   and the propagation of change, which means it’s easier to keep the UI
   and the state synchronized in real-time.

2. **Say no to imperative MVI**: MVI (Model-View-Intent) can often involve
   an “imperative” way of coding, which means telling the program how to
   do things step by step. This can make the code rigid and difficult to
   follow.

    - **Imperative MVI**: This refers to the approach in MVI architecture
      where you have to explicitly manage the flow of data and how the
      state is updated. For example, you may find yourself manually
      setting up a series of state transitions or mutations based on
      different user actions or events.

Varadi suggests avoiding this imperative approach. Instead, he advocates
for using reactive programming constructs, where you would define the
logic for how the state should react to changes in the data, and then let
the framework take care of actually updating the state. This is more about
setting up rules for how the state changes, and less about managing the
details of those changes.

When Varadi mentions not using`MutableStateFlow<UiState>`, he’s pointing
out that you should not necessarily use mutable states (which you
continuously update in an imperative style), but rather employ a more
declarative reactiveness where you combine multiple streams of data that
naturally handle their own changes. Thus, the state updates become more
streamlined and you’re not manually manipulating state variables or
dealing with a series of when statements to handle different actions.
