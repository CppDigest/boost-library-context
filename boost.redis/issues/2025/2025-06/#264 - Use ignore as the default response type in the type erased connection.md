# #264 - Use ignore as the default response type in the type erased connection [Closed]

> Username: mzimbres  
> Created at: 2025-06-05 08:18:08 UTC  
> Updated at: 2025-06-07 11:21:21 UTC  
> Closed at: 2025-06-07 11:21:21 UTC  
> Url: https://github.com/boostorg/redis/issues/264  

The basic_connection uses `ignore` as the [default response type](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L665)  
  
```cpp  
   template <  
      class Response = ignore_t,  
      class CompletionToken = ...>  
   auto async_exec(request const& req, Response& resp = ignore, CompletionToken&& token = {});  
```  
  
the [type erased connection](https://github.com/boostorg/redis/blob/0c8c6fcc095ff103e9adda708c1efb06fabfe08d/include/boost/redis/connection.hpp#L901) however does not  
```cpp  
   template <class Response, class CompletionToken = ...>  
   auto async_exec(request const& req, Response& resp, CompletionToken&& token = {});  
```
