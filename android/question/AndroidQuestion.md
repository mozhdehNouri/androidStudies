###### When only onDestroy is called for an activity without onPause() and onStop()?

answer :

when you calling finish() in onCreate function only onDestory is calling.

consider you doing some opration in onCreate function after all you calling finish()

```kt
class MainActivity():AppCompatActivity(){

override fun onCreate(){
  setContentView()

 // some code
//some opration 
finish()    
}
  }
```
