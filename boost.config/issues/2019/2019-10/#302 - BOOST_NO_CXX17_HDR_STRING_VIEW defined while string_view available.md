# #302 - BOOST_NO_CXX17_HDR_STRING_VIEW defined while string_view available [Closed]

> Username: raffienficiaud  
> Created at: 2019-10-26 19:30:59 UTC  
> Updated at: 2019-10-28 22:22:04 UTC  
> Closed at: 2019-10-28 17:59:56 UTC  
> Url: https://github.com/boostorg/config/issues/302  

Using `clang-8` on Ubuntu 18.04, I am having `BOOST_NO_CXX17_HDR_STRING_VIEW` defined in Boost.Test while it looks to me that this is properly supported by the toolchain.  
  
I am compiling the program with this:  
  
```  
cd libs/test/test  
../../../b2 address-model=64 cxxstd=17 toolset=clang-8.0 basic_cstring-test  
```  
  
In the program, I added this:  
```  
#include <string_view>  
  
#if defined(BOOST_NO_CXX17_HDR_STRING_VIEW)  
#error no hdr defined  
#endif  
```  
and the error fires on the `#error` pragma.  
  
The location where is defined is in `boost/config/stdlib/libstdcpp3.hpp`, in the section (line 300):  
  
```  
#if (BOOST_LIBSTDCXX_VERSION < 70100) || (__cplusplus <= 201402L)  
  
/// added by me for debugging  
#ifdef __clang__  
// clang is defined  
#endif  
  
#if (BOOST_LIBSTDCXX_VERSION < 70100)  
// this is defined  
#endif  
  
#error found where defined  
/// added by me for debugging - end  
  
#  define BOOST_NO_CXX17_STD_INVOKE  
#  define BOOST_NO_CXX17_STD_APPLY  
#  define BOOST_NO_CXX17_HDR_OPTIONAL  
#  define BOOST_NO_CXX17_HDR_STRING_VIEW  
#  define BOOST_NO_CXX17_HDR_VARIANT  
#endif  
```  
  
Is this intentional? What would be the correct way to compile with `string_view` support?  
Thanks,  
Raffi

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-10-26 19:50:29 UTC  
> Url: https://github.com/boostorg/config/issues/302#issuecomment-546634091  

it's a bug: it's damn hard to detect the libstdc++ version when the compiler isn't gcc, and it's detecting gcc-6 libstdc++ because the file need updating :(  
  
It'll be a few days before I get to this....

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-10-28 22:22:03 UTC  
> Url: https://github.com/boostorg/config/issues/302#issuecomment-547170823  

Thanks!
