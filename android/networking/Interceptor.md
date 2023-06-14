### How to define a custom Interceptor :



Interceptors, are a powerful mechanism that can monitor, rewrite, and retry the API call.

when we make an API call, we can either monitor it or perform some tasks.

Interceptors function similarly to airport security personnel during the security check process. They checked our boarding pass, stamped it, and then let us pass.

We can use interceptors to do a variety of things, such as centrally monitoring API calls.



##### Interceptor Types

Interceptors are classified into two types:

1. Interceptors added between the Application Code (our written code) and the OkHttp Core Library are referred to as application interceptors. These are the interceptors that we add with addInterceptor().
2. Interceptors on the network: These are interceptors placed between the OkHttp Core Library and the server. These can be added to OkHttpClient by using the addNetworkInterceptor command ().



##### include interceptors in OkHttpClient

We can add the interceptor while building the OkHttpClient object, as shown below :

```kt
fun gfgHttpClient(): OkHttpClient {
     val builder = OkHttpClient().newBuilder()
         .addInterceptor(/*our interceptor*/)
     return builder.build()
 }
```

and we want to  add a Custom interceptor 
our use case is Record Log ServerError for example our Request face with 401 is Unauthorized and we want when the request face with this error code we want to show for example an error dialog 

```kt
class gfgInterceptor : Interceptor {
	override fun intercept(chain: Interceptor.Chain): Response {
		val aRequest: Request = chain.request()
		val aResponse = chain.proceed(request)
		when (response.code()) {
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

1. First, we obtain the request from the chain. request()

2. The response from the server is then obtained by passing the request through the chain.

3. Proceed(request) At this point, we can check for the response code and take action.

4. Pass this and wait for the results







































Resource :

[OkHttp Interceptor in Android - GeeksforGeeks](https://www.geeksforgeeks.org/okhttp-interceptor-in-android/)
