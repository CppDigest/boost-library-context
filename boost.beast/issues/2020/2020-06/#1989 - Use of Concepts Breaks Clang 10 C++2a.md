# #1989 - Use of Concepts Breaks Clang 10 C++2a [Closed]

> Username: ricejasonf  
> Created at: 2020-06-17 23:45:18 UTC  
> Updated at: 2020-06-18 21:09:56 UTC  
> Closed at: 2020-06-18 21:09:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1989  

I'm getting odd compiler errors when compiling with C++2a on Clang 10. The diagnostic is definitely buggy, but perhaps there is a misuse of the concepts feature (idk).  
  
  
**Boost 1.73.0**: https://godbolt.org/z/cJju9g  
  
```  
In file included from /celibs/boost_1_73_0/boost/beast/core/basic_stream.hpp:1457:  
  
/celibs/boost_1_73_0/boost/beast/core/impl/basic_stream.hpp:782:1: error: out-of-line definition of 'async_connect' does not match any declaration in 'basic_stream<type-parameter-0-0, type-parameter-0-1, type-parameter-0-2>'  
  
async_connect(  
  
^~~~~~~~~~~~~  
```  
  
  
  
**Boost 1.72.0**: https://godbolt.org/z/4eD3QJ  
(Link says Clang trunk but I tried both)  
On 1.72.0 there is a possibly more useful error:  
```  
In file included from /celibs/boost_1_72_0/boost/beast/core/basic_stream.hpp:14:  
  
In file included from /celibs/boost_1_72_0/boost/beast/core/detail/stream_base.hpp:13:  
  
In file included from /celibs/boost_1_72_0/boost/asio/steady_timer.hpp:22:  
  
In file included from /celibs/boost_1_72_0/boost/asio/basic_waitable_timer.hpp:21:  
  
In file included from /celibs/boost_1_72_0/boost/asio/detail/deadline_timer_service.hpp:21:  
  
In file included from /celibs/boost_1_72_0/boost/asio/execution_context.hpp:409:  
  
In file included from /celibs/boost_1_72_0/boost/asio/impl/execution_context.hpp:18:  
  
In file included from /celibs/boost_1_72_0/boost/asio/detail/handler_type_requirements.hpp:53:  
  
/celibs/boost_1_72_0/boost/asio/async_result.hpp:70:20: error: concept cannot have associated constraints  
  
BOOST_ASIO_CONCEPT completion_handler_for =  
```  
  
It works fine on 1.71

---

## Comment 1

> Username: ricejasonf  
> Created at: 2020-06-18 00:09:57 UTC  
> Updated at: 2020-06-18 00:14:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1989#issuecomment-645690769  

```  
template <typename T>  
concept True = true;  
  
template <typename>  
struct basic_stream {  
template<True H>  
  void async_connect();  
};  
  
  
// impl  
  
template<class A>  
template<True H>  
   //    ^ instead of `class`  
void basic_stream<A>::async_connect() {   
```  
  
https://godbolt.org/z/jsaAGY  
  
The problem is that in Beast the function implementations do not also specify the concept in the template function parameter list. Whether the standard requires that I do not know, but it at least makes for a clean work around.  
  
Edit: Err. It appears to be added in the `develop` branch.

---

## Comment 2

> Username: ricejasonf  
> Created at: 2020-06-18 21:09:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1989#issuecomment-646307251  

This was already fixed in `develop` 638e2d1244a3655e90f343c45ef27ff562540b61  
  
Thank you.
