# Comooisition

##### how can you see the reusability of code?

## Prefer composition to inheritance

You may first think of “reuse” as “copying code.” Copying seems like an easy solution,
but it doesn’t work very well. As time passes, your needs evolve. Applying changes
to code that’s been copied is a maintenance nightmare. Did you find all the copies?
Did you make the changes the same way for each copy? Reused code can be changed
in just one place

Inheritance is one way to achieve this. Inheritance creates a new class as a type of
an existing class. You add code to the form of the existing class without modifying
the original. Inheritance is a cornerstone of object-oriented programming.

You can also choose a more straightforward approach, by creating objects of existing
classes inside your new class. This is called composition, because the new class is
composed of objects of existing classes. You’re reusing the functionality of the code,
not its form.

Composition is a has-a relationship. “A house is a building and has a kitchen”

Inheritance describes an is-a relationship, a

what is the meaning of the above example ?

**Composition (has-a relationship)**:

- In composition, you create a class by combining one or more other classes. This relationship is often described as "has-a."
- For example, consider the statement: "A house is a building and has a kitchen."
- In this context, a "house" is composed of a "building" and has a "kitchen." This means that a "house" contains or is made up of a "building" and also includes a "kitchen."

```kt
class Building {
    // ...
}

class Kitchen {
    // ...
}

class House {
    val building = Building()
    val kitchen = Kitchen()
}
```

**Inheritance (is-a relationship)**:

- In inheritance, you create a new class that is a specialized version of an existing class. This relationship is often described as "is-a."
- For example, consider the statement: "A house is a building."
- In this context, a "house" is a specific type of "building." It inherits properties and behaviors from the more general "building" class.

```kt
open class Building {
    // Common properties and methods for buildings
}

class House : Building() {
    // Specific properties and methods for houses
}
```

Composition produces simpler designs and implementations. This doesn’t mean you
should avoid inheritance. It’s just that we tend to get bound up in more complicated
relationships

Composition appears trivial, but is powerful. When a class grows and becomes
responsible for different unrelated things, composition helps pull them apart. Use
composition to simplify the complicated logic of a class.

look at this example

```kt
class Features {
fun f1() = "feature1"
fun f2() = "feature2"
}
class Form {
private val features = Features()
fun operation1() =
features.f2() + features.f1()
fun operation2() =
features.f1() + features.f2()
}
```

The Features class provides implementations for the operations of Form, but the
client programmer who uses Form has no access to features—indeed, the user is
effectively unaware of how Form is implemented. This means that if you find a better
way to implement Form, you can remove features and change to the new approach
without any impact on code that calls Form.
