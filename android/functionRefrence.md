Make your Code readable with **Method Reference**

Look at this example :

```kt
@Composable
fun CommentScreen(backAction:()->Unit){
TopAppBar(
            navigationIcon = {
                IconButton(onClick = backAction) {
                    Icon(Icons.Filled.ArrowBack, null)
                }
            })
}
```

I wanna when user press back button i navigateUp

now i can implement of 2 ways

1- **Method Reference**

```kt
CommentScreen(
   backAction = navController::navigateUp
)
```

2 - **Lambda Expression**

```kt
CommentScreen(
   backAction = {navController.navigateUp()}
)
```

they seem to achieve a similar result, but there are differences in terms of syntax and how the
actions are represented.

Method Reference :

- This syntax is a shorthand for a lambda expression. It's often referred to as a method reference.
- It's concise and can be used when the lambda expression has only one method, which matches the
  method signature of the functional interface.
- In this case, it is equivalent to providing a lambda expression
  like `onNavUp = { navController.navigateUp() }`, but it's more concise.

Lambda Expression:

- This is a more explicit way of defining a lambda expression.
- It allows you to provide additional logic within the lambda block if needed.
- It's useful when you want to perform more than one operation inside the lambda or if you need to
  handle parameters.

for example :

```kt
CommentScreen(
   backAction = {  navController.navigateUp()
          removeUserData()
          // or something else
    }
 )
```
