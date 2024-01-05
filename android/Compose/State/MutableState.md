#### MutableStateOf under the hood in compose

To remind : mutableStateOf will return a`MutableState<T>`object and Compose will keep track of
changes and update the UI whenever you modify the value

In compose if you want to survive of recomposition you have to use mutableStateOf in remember block.

```kt
val text = remember{ mutableStateOf(false)}
```

but how it works under the hood?

Let's Look at mutableState strucutre

```kt
fun <T> mutableStateOf(
    value: T,
    policy: SnapshotMutationPolicy<T> = structuralEqualityPolicy()
)
```

We always set the value for the first item and don't care about second item because it have a
default value.
second parameter is`policy:SnapShotMutationPolicy`

The compose Runtime keeps track of**MutableState**object in current composition means if you change
the value held by**MutableState** compose runtime will notice the change and schedule a
recomposition that is where the second parameter`policy`comes, compose uses it to check if the value
is changed.

##### look at structuralEqualityPoli structural

```kt
fun <T> structuralEqualityPolicy(): SnapshotMutationPolicy<T> =
    StructuralEqualityPolicy as SnapshotMutationPolicy<T>

private object StructuralEqualityPolicy : SnapshotMutationPolicy<Any?> {
    override fun equivalent(a: Any?, b: Any?) = a == b

    override fun toString() = "StructuralEqualityPolicy"
}
```

As you can see`stucuralEqualityPolicy()`returns`SnapshotMutationPolicy<T>`it has an`equivalent()`
method which receives values`a`and`b`one of them is new value we are setting one is old value held
by`MutableState<T>`these values are hopefully supplied by Compose Runtime.

`equivalent()`just checks`a != b`if`true`compose will schedule the recomposition otherwise not.




