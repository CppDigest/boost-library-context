# #2744 - Please merge Boost.Beast to include fixes for GCC 13 [Closed]

> Username: thejohnfreeman  
> Created at: 2023-09-22 12:57:11 UTC  
> Updated at: 2023-10-21 11:12:57 UTC  
> Closed at: 2023-10-21 11:12:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2744  

Here is my minimal test program:  
  
```cpp  
#include <boost/beast/http/message.hpp>  
int main() {  
    return 0;  
}  
```  
  
Here is a sample of the errors (just a sample because they cascade):  
  
```  
In file included from /home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/field.hpp:411,  
                 from /home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/fields.hpp:16,  
                 from /home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/message.hpp:14,  
                 from /home/jfreeman/code/bug-beast-gcc-13/src/bug-beast-gcc-13.cpp:1:  
/home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/impl/field.ipp:30:10: error: ‘uint32_t’ in namespace ‘std’ does not name a type; did you mean ‘wint_t’?  
   30 |     std::uint32_t  
      |          ^~~~~~~~  
      |          wint_t  
/home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/impl/field.ipp:50:10: error: ‘uint32_t’ in namespace ‘std’ does not name a type; did you mean ‘wint_t’?  
   50 |     std::uint32_t  
      |          ^~~~~~~~  
      |          wint_t  
/home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/impl/field.ipp: In static member function ‘static bool boost::beast::http::detail::field_table::equals(boost::beast::string_view, boost::beast::string_view)’:  
/home/jfreeman/.conan/data/boost/1.83.0/.../include/boost/beast/http/impl/field.ipp:83:26: error: ‘uint32_t’ in namespace ‘std’ does not name a type; did you mean ‘wint_t’?  
   83 |         using Int = std::uint32_t; // VFALCO std::size_t?  
      |                          ^~~~~~~~  
      |                          wint_t  
```  
  
The header `boost/beast/http/impl/field.ipp` (and possibly others) needs to start including `<cstdint>` directly. [GCC 13 migration documentation](https://gcc.gnu.org/gcc-13/porting_to.html) says this:  
  
> Some C++ Standard Library headers have been changed to no longer include other headers that were being used internally by the library. As such, C++ programs that used standard library components without including the right headers will no longer compile.  
>  
> The following headers are used less widely in libstdc++ and may need to be included explicitly when compiling with GCC 13:  
>  
> - \<string> (for std::string, std::to_string, std::stoi etc.)  
> - \<system_error> (for std::error_code, std::error_category, std::system_error).  
> - **\<cstdint> (for std::int8_t, std::int32_t etc.)**  
> - \<cstdio> (for std::printf, std::fopen etc.)  
> - \<cstdlib> (for std::strtol, std::malloc etc.)   
  
I have tested with both Boost 1.82 and 1.83. Both exhibit the same error. Compiler is GCC 13.1.0 on Ubuntu 23.04.  
  
The issue was [fixed](https://github.com/boostorg/beast/commit/cd6b79db5a462b0d45af9de0b4989bdbd36ae720) in Boost.Beast sources 4 months ago, but the fix did not make it into Boost 1.83. Boost [master branch](https://github.com/boostorg/boost/tree/master/libs) still points to Boost.Beast commit [c316c6b](https://github.com/boostorg/beast/tree/c316c6bd3571991aeac65f0fc35fca9067bc7906), which is the 4th ancestor of the fix, committed 2 days before.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-25 00:47:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1732732673  

That was my fault. I'll make sure it's in 1.84.

---

## Comment 2

> Username: jwakely  
> Created at: 2023-10-11 14:23:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1757807519  

> The issue was [fixed](https://github.com/boostorg/beast/commit/cd6b79db5a462b0d45af9de0b4989bdbd36ae720) in Boost.Beast sources 4 months ago, but the fix did not make it into Boost 1.83.  
  
This. Keeps. Happening.  
  
Why does Boost use a git workflow where important changes have to be manually cherry picked into the branch that gets released? It keeps going wrong.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2023-10-11 16:12:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1758039185  

That's a great question. Klemens.

---

## Comment 4

> Username: jwakely  
> Created at: 2023-10-11 18:30:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1758269673  

> This. Keeps. Happening.  
  
To be clear, this isn't specific to Beast. It happens with all boost libs.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-10-11 21:47:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1758593545  

It doesn't happen to Boost.JSON, Boost.URL, or Boost.StaticString, which are my other libraries.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2023-10-12 05:37:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1758941043  

Do they maintain two versioning schemes like beast?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2023-10-21 07:33:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1773706335  

Why isn't this fixed yet?

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2023-10-21 10:07:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2744#issuecomment-1773741504  

It is in master.
