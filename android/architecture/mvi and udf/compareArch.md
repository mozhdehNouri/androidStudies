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

action comes into store that action is sent off to the reducer with the current state and then using
those two pieces , the current state and the action.

the reducer spits out a new state and then that state gets updated and passed back.

for example the reducer could take in a current state which is like loading state and then a new
action like preferences loaded and then it will output a new state saying show the date here are the
preferences to show.

// watch again this movie the boy you do not like it.
