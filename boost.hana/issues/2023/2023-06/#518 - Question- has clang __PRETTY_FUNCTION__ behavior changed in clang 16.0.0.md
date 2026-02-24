# #518 - Question: has clang __PRETTY_FUNCTION__ behavior changed in clang 16.0.0 ? [Open]

> Username: dkrejsa  
> Created at: 2023-06-20 22:02:59 UTC  
> Updated at: 2023-06-20 22:24:33 UTC  
> Url: https://github.com/boostorg/hana/issues/518  

Hi,  
  
This is a question relating to the experimental/type_name.hpp implementation with Clang.  
  
I'm using Boost 1.81 and seeing a compilation failure building the hana experimental/type_name test using Clang 16.0.0,  
albeit in a cross-build environment for VxWorks. I build with -std=c++14.  
  
Part of the issue seems to be that for the `boost::hana::experimental::detail::type_name_impl2<int>()` function, clang is yielding  
  
    "cstring boost::hana::experimental::detail::type_name_impl2() [T = int]"  
  
for `__PRETTY_FUNCTION__`, rather than yielding  
  
    "boost::hana::experimental::detail::cstring boost::hana::experimental::detail::type_name_impl2() [T = int]"  
  
as type_name.hpp seems to expect.  That is, the "cstring" return type in the `__PRETTY_FUNCTION__` I see omits the namespace path "boost::hana::experimental::detail::".  
  
Is this something new with Clang 16.0.0, or is it perhaps an issue of some option affecting the compiler behavior, that might be different in my environment?   Any advice appreciated.

---

## Comment 1

> Username: dkrejsa  
> Created at: 2023-06-20 22:17:06 UTC  
> Updated at: 2023-06-20 22:24:33 UTC  
> Url: https://github.com/boostorg/hana/issues/518#issuecomment-1599646649  

Here's an example on godbolt, with Clang 16.0.0  
  
https://godbolt.org/z/3rbaf4Ynv  
  
and with Clang 15.0.0  
  
https://godbolt.org/z/9P7eeWh8a  
  
OK, I was worried this might be restricted to my environment, but it seems to be more general.
