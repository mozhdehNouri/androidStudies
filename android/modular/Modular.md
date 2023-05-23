### Android Modularization

###### Modules

A *module* is a collection of source files and build settings that let you divide your project into discrete units of functionality. Your project can have one or many modules, and one module can use another module as a dependency. You can independently build, test, and debug each module.







###### what is Modularization :

Modularization is a means of structuring your codebase in a way that improves maintainability and helps avoid these problems.

ModularizationModularization is a practice of organizing a codebase into loosely coupled and self contained parts. Each part is a module. Each module is independent and serves a clear purpose. By dividing a problem into smaller and easier to solve subproblems, you reduce the complexity of designing and maintaining a large system.

##### Benefits of Modular :

- Testability

- **Scalability** : In a tightly coupled codebase, a single change can trigger a cascade of alterations. A properly modularized project will embrace the [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) principle. This in turn empowers the contributors with more autonomy while also enforcing architectural patterns.

- **Enabling work in parallel** : Modularization helps decrease version control conflicts and enables more efficient work in parallel for developers in larger teams.

- **Ownership** - A module can have a dedicated owner who is responsible for maintaining the code and tests, fixing bugs, and reviewing changes.

- **Encapsulation** - Isolated code is easier to read, understand, test and maintain.

- **Reduced build time** - Leveraging Gradle’s parallel and incremental build can reduce build times.

- - Highly simplified, Gradle does two things to speed up builds:
    
    1. Cache work it did before so it doesn’t have to do it again
    2. Try to do as much work as possible in parallel
    
    Both of these strategies aren’t effective for single module apps as every code change, makes it impossible to reuse the already generated (cached) compiled code artifact and hence all code has to be recompiled again sequentially.
    
    With multiple modules, however, Gradle can build several modules in parallel and avoid building modules that have no code changed it already has a cached artifact for. This speeds up your incremental builds and even your clean builds if you use the [Gradle build cache](https://gradle.com/build-cache/).

- **Dynamic delivery** - Modularization is a requirement for [Play Feature Delivery](https://developer.android.com/guide/playcore/feature-delivery) which allows certain features of your app to be delivered conditionally or downloaded on demand.

- **Reusability** - Proper modularization enables opportunities for code sharing and building multiple apps, across different platforms, from the same foundation.

- Enables refactoring 

### Modularization pitfalls

However, modularization is a pattern that can be misused, and there are some gotchas to be aware of when modularizing an app:

**Not enough modules** - conversely if your modules are few, large and tightly coupled, you end up with yet another monolith. This means you lose some benefits of modularization. If your module is bloated and has no single, well defined purpose, you should consider splitting it.

**Too complex** - there is no silver bullet here. In fact it doesn’t always make sense to modularize your project. A dominating factor is the size and relative complexity of the codebase. If your project is not expected to grow beyond a certain threshold, the scalability and build time gains won’t apply.

##### Modularization strategy:

- **Low coupling** - Modules should be as independent as possible from one another, so that changes to one module have zero or minimal impact on other modules. They should not possess knowledge of the inner workings of other modules.

- **High cohesion** - A module should comprise a collection of code that acts as a system. It should have clearly defined responsibilities and stay within boundaries of certain domain knowledge. For example, the `core:network` module in Now in Android is responsible for making network requests, handling responses from a remote data source, and supplying data to other modules.

#### what is a module :

A module is **a collection of source files and build settings that let you divide your project into discrete units of functionality**. Your project can have one or many modules, and one module can use another module as a dependency. You can independently build, test, and debug each module.

Modularization is an important aspect of building large-scale applications. It allows developers to break down an application into smaller, more manageable pieces that can be developed, tested, and maintained independently. In the context of Android development, modularization can help improve build times, reduce code complexity, and make it easier to add new features and functionality to an application

There are several approaches to modularization in Android, including modularization by layer, modularization by feature, and hybrid approaches that combine these two approaches.

#### Modularization by Layer :

Modularization by layer involves breaking down an application into separate modules based on the different layers of the application architecture. For example, an Android application typically has a presentation layer (UI), a domain layer (business logic), and a data layer (data access). Each of these layers can be modularized into separate modules that can be developed and tested independently.

- Fits for large projects/teams
- Highly modularized
- Features can be used across multiple apps easily
- Communication between feature modules may be struggling
- More strict rules

With this approach you will have too many modules. The **app** module in the second approach is divided into feature modules. And some shared codes between features are also moved to a separate module

#### Modularization by Feature:

Modularization by feature involves breaking down an application into separate modules based on the different features or use cases of the application. For example, an e-commerce application might have separate modules for product search, product details, shopping cart, and checkout.

Modularization by feature can help improve code organization and make it easier to add new features and functionality to an application. However, it can also lead to duplication of code and dependencies between different feature modules.

#### Hybrid Approaches :

Hybrid approaches combine modularization by layer and modularization by feature. For example, an application might have separate modules for the presentation layer, domain layer, and data layer, but also have separate modules for each feature or use case of the application.

Hybrid approaches can help balance the benefits of modularization by layer and modularization by feature, but can also lead to increased complexity and dependencies between modules.

##### Conclusion

Modularization is an important aspect of building large-scale Android applications. There are several approaches to modularization, including modularization by layer, modularization by feature, and hybrid approaches that combine these two approaches. Each approach has its own benefits and drawbacks, and the choice of approach depends on the specific needs and requirements of the application being developed.

##### Layer vs. Feature Separation

One of the most common ways of Modularization in the old days was to separate the presentation and data layer (aka domain-data-presentation). It took the developers a while to find out that the domain-data-presentation layer was no longer enough to apply Modularization. Separating only these layers will target the small projects, and you wouldn’t benefit from the reasons mentioned above. Because when you develop a Feature, you will touch all the layers, and guess what? You will turn a lot of heads, and all of your layers will recompile every time.

That’s why I want to demonstrate how the incremental build works in the next section. You may be surprised that this layering in your project (without Feature separation) wouldn’t help you have isolated Features.

By incremental build, the build process can run independently in a shorter time than when you only have three modules for your data, domain, and presentation of the entire app. Instead, by Feature separation, you will have more isolated modules, and then you can apply these layers on each Feature module if you are interested. You can read more about these topics [here](https://martinfowler.com/bliki/PresentationDomainDataLayering.html) and [here](https://markonovakovic.medium.com/clean-architecture-is-not-domain-data-presentation-e368d7ff8579).

###### Don't Do These Fatal Mistakes With a Multi-Module Architecture :

each module can build independently using gradle .

each module can build in parallel and it only rebuild the modulas that actually change and that make the build a little faster.

##### mistake 1:

the first mistake you can do  it is you modular your project at first.  

There is no need to make your project modular if it is not necessary. the most of time your project no need to be modular because your project is small and when you modular it you just add complexity in your projet.

##### mistake 2 :

never use of layer-base approch to module your project .

let's Comparison the modular approch benifit will pass in layer-base or not :

the first benfit is build time :

UI module depends on the domain module and the domain module depend on data module 
if any change happens data module domain and ui module rebuild 

the second reusability :

you can't separate the data module and put it in another project.

third benefit delegation :

broke your code you cant use for example team A worked on Module A and Team B work on Module B 

the best practice is to use a hybrid approach for modularize :

first separate module by feature and in the module you have different modules and separate by layer

Resource :

https://developer.android.com/topic/modularization   check this again 

[Android Modularization Preps: Things to know Before Modularizing Your App - droidcon](https://www.droidcon.com/2022/02/15/android-modularization-preps-things-to-know-before-modularizing-your-app/)

https://www.youtube.com/watch?v=p7-AffMucBw

[nowinandroid/ArchitectureLearningJourney.md at main · android/nowinandroid · GitHub](https://github.com/android/nowinandroid/blob/main/docs/ArchitectureLearningJourney.md)

https://medium.com/android-bits/android-app-modularization-tips-d1509e1dea5c

https://developer.android.com/studio/projects#ApplicationModules

----

question :

1- the best and optimize way modularing 

2- why my modular make  my gradle build long

3- how modular can help to build time ?
