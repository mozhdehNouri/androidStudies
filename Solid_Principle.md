#### Understanding SOLID Principles in Android Development

Have you ever found yourself navigating through the code you were working on for several days, trying to fix some sneaky bug you couldn’t find? Have you ever felt disappointed with the amount of time you spent when adding some new features to your code? 

As developers, we must acquaint ourselves with and apply a set of fundamental coding principles known as programming or software development principles. Among these principles, SOLID stands out as a crucial foundation for writing maintainable and scalable code.

**SOLID Principles**

SOLID is an acronym representing five essential principles that guide software development:

1. **SRP - Single Responsibility Principle**

2. **OCP - Open/Closed Principle**

3. **LSP - Liskov Substitution Principle**

4. **ISP - Interface Segregation Principle**

5. **DIP - Dependency Inversion Principle**

6. SRP - Single Responsibility 
   
   The Single Responsibility Principle (SRP) states that a class should have only one `reason to change`, meaning that it should have `only one responsibility.` This principle is fundamental to writing maintainable and scalable software, as it helps to reduce the complexity of a codebase.

the main question is how do we can determine singlw Responsibility what is that mean exactly ?

let me explain with an example 

For instance :

look at this class

```kt
class Employee {
fun calculatePay()
fun save()
fun reportHours() 
}
```

In this class we have 3 methods and our methods do different work
for example reportHours() customer is HR  save() customer is IT and calculatePay() is accounting.
for example HR and IT want to change something and our method of class `intertwined` it possible Sabotage another part of code.

we have 2 important concept and they are Coupling and Cohesion
