# #2044 - Compilation fails under GCC 10.2 with C++20 [Closed]

> Username: claudv  
> Created at: 2020-08-04 18:53:28 UTC  
> Updated at: 2020-08-05 19:57:03 UTC  
> Closed at: 2020-08-05 19:57:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2044  

Hi,  
  
reporting possible issue with latest gcc.  
  
Compiler: gcc 10.2.0 with C++20 enabled (MacOS).  
  
Beast version: 290  
  
How to reproduce: simply include <boost/beast/core.hpp> in a dummy main.cpp file.  
  
Problem: compilation fails in relation to the templated method  
  
 boost::beast::basic_stream<...>::async_connect(....)  
  
Sample error output:  
  
/Users/lhsmarty/code/lib/boost_1_73_0/boost/beast/core/impl/basic_stream.hpp:781:1: error: no declaration matches 'auto boost::beast::basic_stream< <template-parameter-1-1>, <template-parameter-1-2>, <template-parameter-1-3> >::async_connect(const endpoint_type&, ConnectHandler&&)'  
  781 | basic_stream<Protocol, Executor, RatePolicy>::

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-04 19:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2044#issuecomment-668773430  

What happens using the most recent version of Beast or Boost?

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-08-04 20:43:59 UTC  
> Updated at: 2020-08-04 20:48:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2044#issuecomment-668815679  

This is fixed in Boost 1.74 (To be released this week I think).  
  
In the meantime, if you define the preprocessor macro: `BOOST_ASIO_DISABLE_CONCEPTS`, the error will not manifest.  
  
It's due to the fact that clang-9 implemented concepts-ts and gcc10 implements the finalised c++20 concepts and there is a small syntax difference.  
  
Example: https://godbolt.org/z/EE4Pd3

---

## Comment 3

> Username: claudv  
> Created at: 2020-08-05 19:57:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2044#issuecomment-669464458  

Many thanks for your answer.  
  
The macro works, I'll update to latest boost as soon as available.
