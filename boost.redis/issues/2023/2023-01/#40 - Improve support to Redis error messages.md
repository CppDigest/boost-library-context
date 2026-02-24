# #40 - Improve support to Redis error messages [Closed]

> Username: mzimbres  
> Created at: 2023-01-06 16:36:17 UTC  
> Updated at: 2023-02-11 13:56:14 UTC  
> Closed at: 2023-02-11 13:56:14 UTC  
> Url: https://github.com/boostorg/redis/issues/40  

To receive Redis error messages Aedis users must use use `resp3::node` or `std::vector<resp3::node>` as response types. This is to restricting as most users want to receive responses in their final data structure.  
  
One idea to support this is to pass the error as the second parameter of the `adapt` function, so that instead of e.g  
  
`co_await conn->async_exec(req, adapt(resp))`cpp  
  
users would be able to   
  
`co_await conn->async_exec(req, adapt(resp, error))`cpp  
  
and the adapter would store the error in the error variable.

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-01-30 20:14:35 UTC  
> Url: https://github.com/boostorg/redis/issues/40#issuecomment-1409270408  

Before integration in a boost release I want to address the problems mentioned above since they were also pointed out in the Boost review (but not as part of the conditions). Stated more clearly, we need  
  
* A way deliver Redis diagnostic messages to the user.  
* A way to specify that an error in the execution of a specific command is acceptable.  
  
The solution proposed above does not address all concerns pointed by Ruben. The new solution looks like this  
  
- Specifying a response  
  
```cpp  
   // Before.  
   std::tuple<T1, T2, T3, ...> responses;  
  
   // After  
   boost::redis::responses<T1, T2, T3, ...> responses;  
```  
  
- Accessing the response of an element  
  
```cpp  
   // Before  
   auto r = std::get<2>(responses);  
  
   // After  
   auto r = std::get<2>(responses).value;  
```  
- Accessing errors  
  
```cpp  
   // Before: Throws if any command in the pipeline fails and the connection is closed.  
   co_await conn->async_exec(req, adapt(responses));  
  
   // After: User can specify that errros are acceptable for specific commands.  
   std::get<2>(responses).ignore_error = true;  
   co_awat conn->async_exec(req, adapt(responses));  
```  
the error can then be accessed with  
  
```cpp  
   if (std::get<2>(responses).ec) {  
      // The diagnostic is available  
      std::clog << std::get<2>(responses).error_diagnostic << std::endl;  
   }  
   // The connection remains open.  
```  
  
To achieve this `boost::redis::responses` will be defined as  
  
```cpp  
   // Before  
   template <class... Values>  
   using responses = std::tuple<Values...>;  
     
   // After  
   template <class... Values>  
   using responses = std::tuple<response<Values>...>;  
```  
where  
```cpp  
   template <class Value>  
   struct response {  
      bool ignore_error = false;  
      error_code ec;  
      std::string error_diagnostic;  
      Value value;  
   };  
```  
I am also considering add a typedef for generic responses (omitting allocator parameters)  
  
`using generic_responses = std::vector<resp3::node<std::string>>;`

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-02-01 21:20:33 UTC  
> Url: https://github.com/boostorg/redis/issues/40#issuecomment-1412740840  

Klemens proposed to use `system::result` which would make the response above look like  
```cpp  
   template <class Value>  
   struct response {  
      bool ignore_error = false;  
      std::string error_diagnostic;  
      system::result<Value> value;  
   };  
```  
In addition to that, ashtum proposed the elimination of `ignore_error` which would likewise simplify even further  
```cpp  
   template <class Value>  
   struct response {  
      std::string error_diagnostic;  
      system::result<Value> value;  
   };  
```  
This would require however some changes in the behaviour: The connection should not be closed and `async_exec` should always complete with error are a result of a resp3 error. I think this is reasonable and would make `boost::redis` more similar to other redis clients.

---

## Comment 3

> Username: nejati-deriv  
> Created at: 2023-02-03 12:16:55 UTC  
> Updated at: 2023-02-03 15:17:15 UTC  
> Url: https://github.com/boostorg/redis/issues/40#issuecomment-1415794958  

Because the response is either an error or a value there might be a way for using a sum type instead of a struct for the response.  
As a sketch:  
```C++  
struct response_error  
{  
    enum class type  
    {  
        terminated,   // on network error or cancellation  
        simple,  
        blob,  
        unexpected_null  
    };  
  
    type        type;  
    std::string payload;  
};  
  
using response = system::result<Value,response_error>;  
```  
Another alternative for `response_error` can be:  
```C++  
struct response_error  
{  
    error_code ec;  
    std::string payload;  
};  
```
