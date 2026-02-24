# #94 spawn: decay argument types for coro_async_result [Closed]

> Username: cbodley  
> Created at: 2018-03-13 02:36:53 UTC  
> Updated at: 2018-04-10 19:54:55 UTC  
> Closed at: 2018-04-10 19:54:55 UTC  
> Url: https://github.com/boostorg/asio/pull/94  

if the coroutine result type is a reference or has a cv-qualifier (i.e.  
when Arg != decay\<Arg>::type), the async_result<> specialization for  
yield_context fails to compile because the template parameters don't  
match between the base class and its constructor call  
  
this means that initiating functions with signatures like void(T&&)  
and void(error_code, T&&) can't be used with yield_context

---

## Comment 1

> Username: cbodley  
> Created_at: 2018-03-13 02:44:04 UTC  
> Url: https://github.com/boostorg/asio/pull/94#issuecomment-372528138  

a simple reproducer here: https://gist.github.com/cbodley/62c96eefcda46be50c56ca2e0ed84b66  
  
an `async_get_result()` initiator function with `Signature = void(error_code, result_type&&)` fails to compile when passed a `yield_context`:  
```c++  
boost::asio::spawn(context, [&] (boost::asio::yield_context yield) {  
    auto result = async_get_result(context, yield);  
```  
the build log from g++ here: https://gist.github.com/cbodley/299d93a6b6e08e1e20c184cbee726d8e

---
