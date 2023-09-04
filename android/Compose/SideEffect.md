#### Side Effect in compose



essentially a side effect is just everything that escapes the scope of a function 

so for compose epecifically that would mean everything inside composable function that does not have anthing to do with compose so that could be something super simple as  incrementing a variable that is not a state  not a composed state 
but it could also be something like making a network call and putting such side effect into your composable functionsis one of the worst things you can do in compose becuase we dont have control over our composable functions


well we can define theme but the compose framework decides when they are actually called so let's say you would make a network call in your composable function that would mean that on every recomposition and every ui update that network call would fire again and of course that is terrible  you don't want this 

but of course we still want to  be able to make network calls to be able to access our database or just to increment variable that 


