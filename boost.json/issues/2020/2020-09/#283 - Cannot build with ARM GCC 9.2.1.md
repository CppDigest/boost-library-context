# #283 - Cannot build with ARM GCC 9.2.1 [Closed]

> Username: brooksprumo  
> Created at: 2020-09-05 23:28:18 UTC  
> Updated at: 2020-09-07 18:43:13 UTC  
> Closed at: 2020-09-07 18:43:13 UTC  
> Url: https://github.com/boostorg/json/issues/283  

I saw the RFC thread posted on Reddit and got really excited for hopefully a great JSON library that works for the embedded world.  
  
I'm attempting to build with C++17 on ARM with the ARM GCC 9.2.1 toolchain. For my testing, I'm using ARM Mbed OS 5.15.5, but I don't think the OS is playing a part in my issues.  
  
I'm starting off with a simple single-file cpp that includes boost/json, then in main() prints out Hello World and exits. But compilation fails.  
  
I'm defining `BOOST_JSON_STANDALONE`, and I cannot/do not use exceptions.  
  
My first error was:  
  
```  
Compile [  0.2%]: src.cpp  
[Error] memory_resource.hpp@21,5: #error Support for std::memory_resource is required to use Boost.JSON standalone  
```  
which is from:  
  
```  
# if __has_include(<memory_resource>)  
#  include <memory_resource>  
#  if __cpp_lib_memory_resource < 201603L  
#   error Support for std::memory_resource is required to use Boost.JSON standalone  
#  endif  
# else  
#  error Header <memory_resource> is required to use Boost.JSON standalone  
# endif  
```  
In the ARM GCC 9.2.1 `memory_resource`, it has:  
  
```  
namespace std _GLIBCXX_VISIBILITY(default)  
{  
_GLIBCXX_BEGIN_NAMESPACE_VERSION  
namespace pmr  
{  
#ifdef _GLIBCXX_HAS_GTHREADS  
  // Header and all contents are present.  
# define __cpp_lib_memory_resource 201603  
#else  
  // The pmr::synchronized_pool_resource type is missing.  
# define __cpp_lib_memory_resource 1  
#endif  
```  
  
So the `__cpp_lib_memory_resource ` is set to 1 and not 201603, but I *do* have `memory_resource`. *I think this might be a bug*.  
  
If I modify json/memory_resource.hpp to:  
```  
# if __has_include(<memory_resource>)  
#  include <memory_resource>  
# endif  
```  
  
Then the next error is related to exceptions:  
  
```  
[ERROR] In file included from ./lib/json/include/boost/json/src.hpp:30,  
                 from ./lib/json/src/src.cpp:11:  
./lib/json/include/boost/json/impl/array.ipp: In member function 'void boost::json::standalone::array::shrink_to_fit()':  
./lib/json/include/boost/json/impl/array.ipp:282:11: error: exception handling disabled, use '-fexceptions' to enable  
  282 |     catch(...)  
```  
  
Looking at `json/impl/array.ipp` I saw the source of the error:  
  
```  
#ifndef BOOST_NO_EXCEPTIONS  
    }  
    catch(...)  
    {  
        // eat the exception  
        return;  
    }  
#endif  
```  
  
So then I defined `BOOST_NO_EXCEPTIONS` and saw this compilation error:  
  
```  
Compile [  0.2%]: src.cpp  
[Error] except.ipp@24,11: exception handling disabled, use '-fexceptions' to enable  
[Warning] except.ipp@25,1: 'noreturn' function does return  
[Warning] except.ipp@25,1: 'noreturn' function does return  
[Warning] except.ipp@25,1: 'noreturn' function does return  
[Warning] except.ipp@25,1: 'noreturn' function does return  
[Warning] except.ipp@25,1: 'noreturn' function does return  
[ERROR] In file included from ./lib/json/include/boost/json/src.hpp:48,  
                 from ./lib/json/src/src.cpp:11:  
./lib/json/include/boost/json/detail/impl/except.ipp: In instantiation of 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::bad_alloc]':  
./lib/json/include/boost/json/detail/impl/except.ipp:37:30:   required from here  
./lib/json/include/boost/json/detail/impl/except.ipp:24:11: error: exception handling disabled, use '-fexceptions' to enable  
   24 |     throw e;  
      |           ^  
./lib/json/include/boost/json/detail/impl/except.ipp: In function 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::bad_alloc]':  
./lib/json/include/boost/json/detail/impl/except.ipp:25:1: warning: 'noreturn' function does return  
   25 | }  
      | ^  
./lib/json/include/boost/json/detail/impl/except.ipp: In function 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::length_error]':  
./lib/json/include/boost/json/detail/impl/except.ipp:25:1: warning: 'noreturn' function does return  
./lib/json/include/boost/json/detail/impl/except.ipp: In function 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::invalid_argument]':  
./lib/json/include/boost/json/detail/impl/except.ipp:25:1: warning: 'noreturn' function does return  
./lib/json/include/boost/json/detail/impl/except.ipp: In function 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::out_of_range]':  
./lib/json/include/boost/json/detail/impl/except.ipp:25:1: warning: 'noreturn' function does return  
./lib/json/include/boost/json/detail/impl/except.ipp: In function 'void boost::throw_exception(E, const boost::json::standalone::detail::source_location&) [with E = std::system_error]':  
./lib/json/include/boost/json/detail/impl/except.ipp:25:1: warning: 'noreturn' function does return  
```  
  
and `exception.ipp`  says:  
  
```  
#ifdef BOOST_JSON_STANDALONE  
namespace boost {  
template<class E>  
void  
BOOST_NORETURN  
throw_exception(E e,  
    json::detail::source_location const&)  
{  
    throw e;  
}  
} // boost  
#endif  
```  
  
which shows a `throw`, even though my exceptions are turned off. *I think this might be a bug*.  
  
I'm still very interested in trying out Boost.JSON. Please do let me know if I can do anything to help, or provide for information. Thank you for your work!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-05 23:37:55 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687676451  

Well, we don't test with ARM GCC 9.2.1 (or any ARM for that matter) so.... growing pains are expected :) Your report is already pretty good, and I will get cracking on it. But what would really make sure that this architecture is well-supported is for you to add a Travis target to the yml file which targets ARM:  
https://github.com/CPPAlliance/json/blob/develop/.travis.yml

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-09-05 23:46:05 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687677012  

I think you should try the Boost-flavored JSON, if anything to see what happens. In other words do not define `BOOST_JSON_STANDALONE`.

---

## Comment 3

> Username: brooksprumo  
> Created at: 2020-09-06 00:06:28 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687678513  

I'll give both of these a whirl tomorrow. Thanks for the tips!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-09-06 00:08:26 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687678668  

I'm working on adding an ARM target to Travis and fixing the issues you raised:  
https://github.com/CPPAlliance/json/pull/286  
  
Consider participating in the Boost formal review of the library!

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-06 13:15:48 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687786927  

These changes add an ARM target to Travis: https://github.com/CPPAlliance/json/pull/286

---

## Comment 6

> Username: brooksprumo  
> Created at: 2020-09-06 18:13:43 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687859750  

When compiling without defining `BOOST_JSON_STANDALONE`, I get the following compilation error:  
  
```  
Compile [  0.2%]: src.cpp  
[Fatal Error] config.hpp@14,11: boost/config.hpp: No such file or directory  
[ERROR] In file included from ./lib/json/include/boost/json/src.hpp:24,  
                 from ./lib/json/src/src.cpp:11:  
./lib/json/include/boost/json/detail/config.hpp:14:11: fatal error: boost/config.hpp: No such file or directory  
   14 | # include <boost/config.hpp>  
      |           ^~~~~~~~~~~~~~~~~~  
compilation terminated.  
```  
  
I'm guessing normal Boost contains this config.hpp, and since I don't have any other parts of Boost, the config.hpp cannot be found.  
  
This was tested against commit `a25e988c86a0993178658b35f6d1db31adc11124`.

---

## Comment 7

> Username: brooksprumo  
> Created at: 2020-09-06 18:27:55 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687863234  

I checked out commit `34186c5b2b9f84b4331cb6200cebab08140a998f` from the #286 PR and did successfully build! Both `BOOST_JSON_STANDALONE` and `BOOST_NO_EXCEPTIONS` were defined too.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-09-06 18:35:57 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687865109  

There's a test failure having to do with detecting invalid utf-16 escape sequences, but other than that - things should work for you. I'd like to know your experiences please keep us up-to-date!

---

## Comment 9

> Username: brooksprumo  
> Created at: 2020-09-06 18:38:44 UTC  
> Updated at: 2020-09-06 18:44:37 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687865770  

Ah darn, I think I spoke too soon...  
  
I added more code to my example that called `boost::json::value_from()` then called `boost::json::to_string()` and got link errors for undefined references to exception-related stuffs:  
  
```  
Link: json  
/home/wellhead/gcc-arm-none-eabi-9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: BUILD/NRF52840_DK/GCC_ARM-MY_DEVELOP/lib/json/src/src.o: in function `boost::json::standalone::detail::throw_length_error(char const*, boost::json::standalone::detail::source_location const&)':  
/home/wellhead/git/wellaware/json/./lib/json/include/boost/json/basic_parser.hpp:1256: undefined reference to `boost::json::standalone::throw_exception(std::exception const&)'  
collect2: error: ld returned 1 exit status  
[ERROR] /home/wellhead/gcc-arm-none-eabi-9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: BUILD/NRF52840_DK/GCC_ARM-MY_DEVELOP/lib/json/src/src.o: in function `boost::json::standalone::detail::throw_length_error(char const*, boost::json::standalone::detail::source_location const&)':  
/home/wellhead/git/wellaware/json/./lib/json/include/boost/json/basic_parser.hpp:1256: undefined reference to `boost::json::standalone::throw_exception(std::exception const&)'  
collect2: error: ld returned 1 exit status  
```  
  
Now re-building with `BOOST_JSON_HEADER_ONLY` defined. I'll update this comment once it finishes.  
  
Update: Mostly the same results:  
  
```  
Link: json  
/home/wellhead/gcc-arm-none-eabi-9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: BUILD/NRF52840_DK/GCC_ARM-MY_DEVELOP/source/main.o: in function `boost::json::standalone::detail::throw_length_error(char const*, boost::json::standalone::detail::source_location const&)':  
/home/wellhead/git/wellaware/json/./lib/json/include/boost/json/detail/impl/except.ipp:67: undefined reference to `boost::json::standalone::throw_exception(std::exception const&)'  
collect2: error: ld returned 1 exit status  
[ERROR] /home/wellhead/gcc-arm-none-eabi-9-2019-q4-major/bin/../lib/gcc/arm-none-eabi/9.2.1/../../../../arm-none-eabi/bin/ld: BUILD/NRF52840_DK/GCC_ARM-MY_DEVELOP/source/main.o: in function `boost::json::standalone::detail::throw_length_error(char const*, boost::json::standalone::detail::source_location const&)':  
/home/wellhead/git/wellaware/json/./lib/json/include/boost/json/detail/impl/except.ipp:67: undefined reference to `boost::json::standalone::throw_exception(std::exception const&)'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-09-06 18:50:39 UTC  
> Updated at: 2020-09-06 18:51:20 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687868651  

When you define `BOOST_NO_EXCEPTIONS` in standalone, you are responsible for providing the `throw_exception` function:  
https://github.com/CPPAlliance/json/blob/a25e988c86a0993178658b35f6d1db31adc11124/include/boost/json/detail/impl/except.ipp#L28  
  
(However, I'm noticing another bug, `throw_exception` should be in `boost::` not `boost::json::standalone::`, I will fix that shortly)  
  
It is documented in the readme:  
https://github.com/CPPAlliance/json/blob/a25e988c86a0993178658b35f6d1db31adc11124/README.md#without-boost  
  
(and the html docs)

---

## Comment 11

> Username: brooksprumo  
> Created at: 2020-09-06 19:06:43 UTC  
> Url: https://github.com/boostorg/json/issues/283#issuecomment-687872417  

> When you define `BOOST_NO_EXCEPTIONS` in standalone, you are responsible for providing the `throw_exception` function:  
  
Ah, thank you for the pointer! Defining `throw_exception()` fixed my build and my example works!
