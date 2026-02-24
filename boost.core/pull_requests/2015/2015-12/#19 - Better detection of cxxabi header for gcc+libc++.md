# #19 Better detection of cxxabi header for gcc+libc++ [Closed]

> Username: apolukhin  
> Created at: 2015-12-31 11:58:02 UTC  
> Updated at: 2015-12-31 16:52:19 UTC  
> Closed at: 2015-12-31 16:52:19 UTC  
> Url: https://github.com/boostorg/core/pull/19  

Some of the Android related tests run GCC with libc++. On those tests abi::__cxa_demangle is not used, while it is available.  
This pull request allows usage of `__has_include` for latest GCC and assumes that all the libc++ have `cxxabi.h` header.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-12-31 12:12:27 UTC  
> Url: https://github.com/boostorg/core/pull/19#issuecomment-168184077  

1. Your change seems to drop support for clang as it defines `__GNUC__` but not to 5 (to 4).  
2. I'm not sure the presence of libc++ indicates presence of `cxxabi.h`. I'd rather be more conservative and accidentally not perform demangling instead of failing to compile because of the absent header.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-12-31 13:26:08 UTC  
> Url: https://github.com/boostorg/core/pull/19#issuecomment-168193557  

> 1. Your change seems to drop support for clang as it defines **GNUC** but not to 5 (to 4).  
  
Fixed that.  
  
> 1. I'm not sure the presence of libc++ indicates presence of cxxabi.h. I'd rather be more conservative and accidentally not perform demangling instead of failing to compile because of the absent header.  
  
Search for "cxxabi.h libc++" gave no pages that describe relevant bugs. I'd rather commit it in that way and remove the `|| defined( _LIBCPP_VERSION )` in case of any errors in Boost test suite.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-12-31 16:52:19 UTC  
> Url: https://github.com/boostorg/core/pull/19#issuecomment-168222208  

You didn't find anything because apparently noone uses libc++ macros as an indication of `cxxabi.h` presence. This makes sense since, AFAIU, `cxxabi.h` is part of gcc runtime and, if anything, can be considered part of libstdc++. libc++ has no relation to it, an I suspect platforms that run libc++ but don't have `cxxabi.h` are quite real (OS X comes to mind).  
  
So I committed a different change that makes use of `__has_include` on gcc, when possible, but otherwise only use `cxxabi.h` when using libstdc++. If you can guarantee that a particular platform uses libc++ and always have `cxxabi.h` as well then please create a separate PR adding detection of that particular platform (e.g. checking for OS and compiler).

---
