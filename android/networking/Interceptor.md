#### OkHttp Interceptor

### How to define a custom Interceptor :

##### What are Interceptors?

Interceptors, are a powerful mechanism that can monitor, rewrite, and retry the API call.

when we make an API call, we can either monitor it or perform some tasks.

Interceptors function similarly to airport security personnel during the security check process. They checked our boarding pass, stamped it, and then let us pass.

We can use interceptors to do a variety of things, such as centrally monitoring API calls.

##### Interceptor Types

Interceptors are classified into two types:

1. **Application Interceptors**: Interceptors added between the Application Code (our written code) and the OkHttp Core Library are referred to as application interceptors. These are the interceptors that we add with addInterceptor().
2. **Network Interceptors**: Interceptors on the network: These are interceptors placed between the OkHttp Core Library and the server. These can be added to OkHttpClient by using the addNetworkInterceptor command ().

##### include interceptors in OkHttpClient

We can add the interceptor while building the OkHttpClient object, as shown below :

```kt
fun myHttpClient(): OkHttpClient {
     val builder = OkHttpClient().newBuilder()
         .addInterceptor(/*our interceptor*/)
     return builder.build()
 }
```

Here, in `addInterceptor`, we pass the interceptor that we have created. Now, let's see how to create the Interceptor.

## Creating the Interceptor

To create the interceptor, we need to create a class by implementing the Interceptor interface as below:

```kt
class MyInterceptor : Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
        /**
         * Our API Call will be intercepted here
         */
    }
}
```

Here, in the `intercept()`, we can perform any action which we want inside it.

And to use the interceptor, we can use as below:

```kt
fun myHttpClient(): OkHttpClient {
    val builder = OkHttpClient().newBuilder()
        .addInterceptor(MyInterceptor())
    return builder.build()
}
```

##### Common use-cases we use interceptors in Android:

### Logging the errors centrally

```kt
class ErrorInterceptor  : Interceptor {
    override fun intercept(chain: Interceptor.Chain): Response {
        val aRequest: Request = chain.request()
        val aResponse = chain.proceed(request)
        when (aResponse.code()) {
            400 -> {
                // Show Bad Request Error Message
            }
            401 -> {
                // Show UnauthorizedError Message
            }

            403 -> {
                // Show Forbidden Message
            }

            404 -> {
                // Show NotFound Message
            }

            // ... and so on
        }
        return response
    }
}
```

1. First, we get the request from the `chain.request()`

2. Then, we get the response returned from the server, by passing the request in the `chain.proceed(request)`

3. Now, we can check for the response code and perform an action.

4. We can pass the error to the view.

5. Let's say we get a **401 error** i.e. **Unauthorized** then we can perform an action to clear the app data/log out the user or any action which we want to perform.

6. 

```kt
.addInterceptor(ErrorInterceptor())
```

#### How we can keeping response in the cache

Resource :

[OkHttp Interceptor in Android - GeeksforGeeks](https://www.geeksforgeeks.org/okhttp-interceptor-in-android/)

[OkHttp Interceptor](https://amitshekhar.me/blog/okhttp-interceptor)

[OkHttp Interceptor in Android - GeeksforGeeks](https://www.geeksforgeeks.org/okhttp-interceptor-in-android/)

https://amitshekhar.me/blog/caching-with-okhttp-interceptor-and-retrofit
