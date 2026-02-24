# #58 - Fallthrough warning 5262 on msvc in boost/exception/detail/exception_ptr.hpp [Closed]

> Username: dkprice  
> Created at: 2024-10-22 16:44:38 UTC  
> Updated at: 2026-02-04 09:20:38 UTC  
> Closed at: 2024-10-23 18:42:01 UTC  
> Url: https://github.com/boostorg/exception/issues/58  

In msvc, I see fallthrough warnings in exception_ptr.hpp as included by boost/thread.hpp when [warning 5262](https://learn.microsoft.com/en-us/cpp/error-messages/compiler-warnings/c5262?view=msvc-170) is enabled:  
  
  
```  
1_86_0\boost\exception\detail\exception_ptr.hpp(503): error C2220: the following warning is treated as an error  
1_86_0\boost\exception\detail\exception_ptr.hpp(384): warning C5262: implicit fall-through occurs here; are you missing a break statement? Use [[fallthrough]] when a break statement is intentionally omitted between cases  
1_86_0\boost\exception\detail\exception_ptr.hpp(384): note: to simplify migration, consider the temporary use of /Wv:18 flag with the version of the compiler with which you used to build without warnings  
1_86_0\boost\exception\detail\exception_ptr.hpp(383): note: statement that may fall through is here  
```  
  
Note that I have -external:I headers configured for boost, so this should not be flagging at all. I suspect that there may be pragma warning adjustments happening in headers that precede this one that cause this error to arise.  
  
Regardless, a [[fallthrough]], if mainlined, would eliminate this issue.

---

## Comment 1

> Username: zajo  
> Created at: 2024-10-23 06:52:05 UTC  
> Url: https://github.com/boostorg/exception/issues/58#issuecomment-2431069521  

Adding [[fallthrough]] is not at all straight forward, as this is a very old library that may be used with very old compilers. It's possible that there is a Boost compatibility macro for this purpose, I don't know.  
  
If you send an MR which disables C5262 in this header, I'll look at it once all the unit tests are passing.  
  
Regardless, I am reluctant to make changes. If it ain't broken, don't fix it.

---

## Comment 2

> Username: dkprice  
> Created at: 2024-10-23 18:42:01 UTC  
> Url: https://github.com/boostorg/exception/issues/58#issuecomment-2433148305  

Thank you for the comments. Given your reluctance, I will seek a local suppression, and close out this issue.  
  
For anyone else encountering this issue, the root of the issue for me was that this header sets a warning level of 1 via a `#pragma warning(push, 1)` statement, which overrides the external:I warning level tracked by the compiler (as documented [here](https://learn.microsoft.com/en-us/cpp/build/reference/external-external-headers-diagnostics)). That overriding, in combination with a /w15262 flag to the command line, led to the error, because w1 adds 5262 to the level 1 warning level.  
  
The local fix I will apply will set 5262 only on a higher warning level, e.g. /w45262.

---

## Comment 3

> Username: zajo  
> Created at: 2024-10-23 19:35:27 UTC  
> Url: https://github.com/boostorg/exception/issues/58#issuecomment-2433276867  

Thanks, let me know if I can help. I just don't want to break things, I haven't touched this code in years.

---

## Comment 4

> Username: rubenvanboxem  
> Created at: 2026-02-04 09:11:24 UTC  
> Updated at: 2026-02-04 09:20:38 UTC  
> Url: https://github.com/boostorg/exception/issues/58#issuecomment-3846232083  

To fix this, there is BOOST_FALLTHROUGH in Boost.Config:  
https://www.boost.org/doc/libs/latest/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.boost_helper_macros  
  
Newer MSVC versions also seem to know about #pragma system_header:  
https://learn.microsoft.com/en-us/cpp/build/reference/external-external-headers-diagnostics?view=msvc-170#system_header-pragma  
  
but I suppose just solving the warning with BOOST_FALLTHROUGH is the simpler option.
