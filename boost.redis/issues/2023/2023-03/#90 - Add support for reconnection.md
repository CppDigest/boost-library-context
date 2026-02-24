# #90 - Add support for reconnection [Closed]

> Username: mzimbres  
> Created at: 2023-03-20 00:57:21 UTC  
> Updated at: 2023-05-06 13:39:31 UTC  
> Closed at: 2023-05-06 13:39:31 UTC  
> Url: https://github.com/boostorg/redis/issues/90  

The overwhelming majority of Apps that use Redis need to implement reconnection. At the moment Boost.Redis provides only one example about how to do it and that example requires C++20, which means not all users are able to use it, as result people have to implement it over and over again.   
  
We could provide our own implementation to remove the burden from users. My initial idea is a function with the following signature  
  
```cpp  
auto async_connect(conn, parameters, channels, token);  
```  
  
This function would keep `conn` connected to Redis by reconnecting it when the connection is lost. The interface above does not allow a _disable reconnection_ functionality as even when users calls `conn.cancel()` the `async_connect` function above would reconnect. To cancel its execution it would be necessary to bind a cancellation slot.  
  
Alternatively we could provide this functionality via  connector object, which would be used like  
  
```cpp  
connector ctor;  
ctor.async_connect(conn, ...);  
```  
  
so that users can call `ctor.cancel()` followed by `conn.cancel()` to exit the operation.
