## StateFlow or MutableState

when you use compose for build you ui maybe this question has come up use StateFlow or mutable State in your viewModel you manageing ui state and what is best practice

today we want to discuss this topic :

ok let's start 

**first of all, I want to give a little more explanation about mutable state :**

In Compose, the `mutableState` is a property that holds a value and allows you to modify it. When you read the `mutableState` property within a Composable function, it automatically subscribes the current RecomposeScope to any changes of that value.

If you modify the `mutableState` property by writing a new value to it, Compose will schedule a recomposition of any Composables that are subscribed to that `mutableState`. This means that the user interface will be updated to reflect the new value.

However, if you write the same value to the `mutableState` property, Compose will recognize that there's no actual change and won't schedule any recompositions. This optimization avoids unnecessary UI updates.

`mutableState` is a way to hold and modify a value in Compose, and it automatically triggers recomposition and UI updates when the value changes.

```kt
var count by remember { mutableStateOf(0) }
```

#### StateFlow :

`StateFlow` is a type of flow that emits the current and subsequent values, maintaining state and automatically updating its subscribers.

#### We have 3 scenarios in hear to say using which one :

###### Scenario 1 :

saying yes to using StateFlow in ViewModel instead of compose mutableState for 3  reason   :

using mutableState is very simple and decreases  boilerplate code that comes with stateFlow or Shared Flow 
look at this example 

using StateFlow :

```kt
lateinit val _uiState :MutableStateFlow<Student> =mutableStateof()
val uiState:StateFlow<Student> get() = _uiState
```

Using MutableState :

```kt
lateinit val uiState:
mutableState<Student> = mutableStateOf()
      private set
```

1- Flow provides some operator like combine, filter , map ... Something that  but compose mutable state is very simple and  it does not have these features

2- `SaveStateHandle` in android have extension function that return a StateFlow and less than boilerplate code from saving a mutableState value  in SaveStateHandle.

3- reusability : you should keep your viewModel away from compose It causes  your viewModel reusable

for example : if you using compose  mutableState in your viewmodel and if in future you want to move your viewmodel in xml project or kmm you no longer have access to mutableState

###### Senario2 :

using compose MuableState  :

mutable state is a data structure and it use observing data with compose .

it's very simple and nothing  have special properties  and doesn't wait untile the subscriber call collect for  geting data.

if  your purpose is just to observe some data and show it in ui you don't need  to Make it complicated by using state Flow while not using its features  you can use mutableState 

mutable state only emit one value and  it has the capability of observing , making for compose and is optimal.

**if you choose 2 scenario you need to keep reading**

when we use state flow in compose  The `result` property is now a `Flow<String>` and not just a `String`, so `collectAsState` must be used inside the composable to obtain the value (and listen to updates):

```kt
@Composable
fun Calculator(state: CalculatorState) {
    Column {
        TextField(value = state.v1, onValueChange = { state.v1 = it })
        TextField(value = state.v2, onValueChange = { state.v2 = it })
        Text(text = state.result.collectAsState(initial = "").value)
    }
}
```

t works but what happens on a configuration change (for example changing the device orientation)? Unfortunately all the data inserted by the user and an eventual ongoing calculation are lost. Using `rememberSaveable` instead of `remember` in `CalulatorScreen` helps maintaining the values but it doesn’t keep an ongoing calculation alive. The `collectAsState` method under the hood uses the `CoroutineScope` associated with the composable. As we know the `Activity` is destroyed and recreated on a configuration change, so the composable’s `CoroutineScope` is not maintained (and an ongoing calculation created using it is canceled).

The usual solution to these kinds of problems is to use a `ViewModel`. Maybe in the future in a “full” Compose app all the orientation changes will be managed as `State`s, however right now using a `ViewModel` continue to be a good solution.

## ViewModel (and viewModelScope) to the rescue

Many Compose examples that use a `ViewModel` expose the values using `LiveData`s or `MutableStateFlow`s. Let’s see how to create something similar to the previous example using a `ViewModel` that uses `MutableStateFlow`s.

```kt
class CalculatorViewModel : ViewModel() {
    var v1 = MutableStateFlow("0")
    var v2 = MutableStateFlow("0")
    val result = v1.combine(v2) { a, b -> a to b}.mapLatest {
        sum(it.first, it.second)
    }.stateIn(viewModelScope, SharingStarted.Lazily, "0")
}
```

The `CalculatorScreen` must be changed to use `viewModel` instead of `remember`:

```kt
@Composable
fun CalculatorScreen() {
    val viewModel = viewModel<CalculatorViewModel>()
    Calculator(state = viewModel)
}
```

The composable code is a bit more complicated, `collectAsState` and `value` must be used because the properties are `MutableStateFlow`s instead of regular `String` created using a `MutableState`:

```kt
@Composable
fun Calculator(state: CalculatorViewModel) {
    Column {
        TextField(value = state.v1.collectAsState().value, onValueChange = { state.v1.value = it })
        TextField(value = state.v2.collectAsState().value, onValueChange = { state.v2.value = it })
        Text(text = state.result.collectAsState().value)
    }
}
```

It works as expected also on a configuration change! But the code contains a bit of boilerplate, can it be simplified?

## MutableStates in a ViewModel

As discussed before, many Compose examples use `MustableStateFlow`s in the `ViewModel`. It works and can be useful in case the `ViewModel` is used also from a standard View based UI. But what about a `ViewModel` used only by composables? Can it use `MutableState`s to keep the code simple? [This codelab](https://developer.android.com/codelabs/jetpack-compose-state#8) uses this strategy, let’s try to use it in this example as well.

```kt
class CalculatorViewModel : ViewModel() {
    var v1 by mutableStateOf("0")
    var v2 by mutableStateOf("0")
    var result by mutableStateOf("0")
        private set
    init {
        snapshotFlow { v1 to v2 }
            .mapLatest {
                sum(it.first, it.second)
            }
            .onEach {
                result = it
            }
            .launchIn(viewModelScope)
    } 
```

Another advantage of using `MutableState`s is that exposing a read-only value and make it modifiable only inside the class is really easy. Just a `private set` is needed instead of defining two properties (one with an `_` prefix) as it must be done using `MutableStateFlow`s or `LiveData`s.

And now the composable can be changed removing also the last `collectAsState`:

```kt
@Composable
fun Calculator(state: CalculatorViewModel) {
    Column {
        TextField(value = state.v1, onValueChange = { state.v1 = it })
        TextField(value = state.v2, onValueChange = { state.v2 = it })
        Text(text = state.result)
    }
}
```

The code is easy to read and the composable contains only the UI, it’s not aware of anything that happens to calculate the values.



Result :







Resource :

https://developer.android.com/reference/kotlin/androidx/compose/runtime/MutableState

https://proandroiddev.com/viewmodels-using-compose-mutablestateflows-or-mutablestates-64d34ba548c5

https://www.youtube.com/watch?v=T8vApYJlW8o

[Persisting data in ViewModels with MutableState (Jetpack Compose ViewModel Example) - YouTube](https://www.youtube.com/watch?v=QDcBO4iROyE)
