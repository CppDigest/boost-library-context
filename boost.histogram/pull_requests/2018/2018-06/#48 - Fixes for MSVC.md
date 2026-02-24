# #48 Fixes for MSVC [Merged]

> Username: jbuonagurio  
> Created at: 2018-06-28 16:51:10 UTC  
> Updated at: 2018-06-29 11:29:07 UTC  
> Merged at: 2018-06-29 11:29:07 UTC  
> Closed at: 2018-06-29 11:29:07 UTC  
> Url: https://github.com/boostorg/histogram/pull/48  

Here is a first pass at fixing some build issues with MSVC on the develop branch. I am testing with Visual Studio 2017 (19.12.25835) and the v2.0 release has been working nicely. List of issues and comments follows.  
  
**Tests passing:** adaptive_storage_test, array_storage_test, axis_size, detail_test, weight_counter_test  
**Tests failing (will not compile):** axis_test, histogram_test  
  
## Ignore GCC Extensions  
  
This addresses MSVC warning [C4068](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-1-c4068) (unknown pragma) and undeclared identifier errors.  
  
I added some `#ifdef _MSC_VER` guards to make `__attribute__((unused))` a no-op, and disable GCC diagnostic pragmas that have no MSVC equivalent. This could go the other way around as well - definitions could be added specifically for GCC and clang.  
  
`__attribute__((unused))`  could be replaced with C++11 style `[[gnu::unused]]` and newer versions of MSVC shouldn't have a problem with unknown attributes, but this may be the slightly safer route for older compilers.  
  
## _Temporary_ Fix for Built-in Axis Types  
  
I was getting errors [C2976](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-2/compiler-error-c2976) and [C3203](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-2/compiler-error-c3203) when attempting to compile the `builtins` mpl::vector. Brief listing follows; the same error was reported for the regular, circular, variable, integer and category axis types:  
  
```  
histogram_fwd.hpp(39): error C2976: 'boost::histogram::axis::regular': too few template arguments  
histogram_fwd.hpp(44): error C3203: 'regular': unspecialized class template can't be used as a template argument for template parameter 'T0', expected a real type  
...  
```  
  
I'm new to template metaprogramming so not sure whether MSVC is in the wrong here. As a workaround, I explicitly specialized all types. This may mean that the 'any' class loses genericity, but it only had options for built-in types anyway. Would a user-defined axis class that didn't derive from a built-in type work with `any`, for example `axis_base` or `axis_base_uoflow`?  
  
This is temporary because the **axis_test** and **histogram_test** are failing with a long list of error messages that I think are all ultimately related to `any`. I don't see any 'quick fix' at the moment.  
  
## Type Deduction Fix  
  
This addresses MSVC error [C3538](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-2/compiler-error-c3538), output follows:  
  
```  
adaptive_storage.hpp(466): error C3538: in a declarator-list 'auto' must always deduce to the same type  
adaptive_storage.hpp(466): note: could be 'unsigned long'  
adaptive_storage.hpp(466): note: or '::size_t'  
```  
  
I just swapped out the `auto` for `decltype(size())`.

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-06-28 17:03:37 UTC  
> Url: https://github.com/boostorg/histogram/pull/48#issuecomment-401105266  

[![Coverage Status](https://coveralls.io/builds/17741480/badge)](https://coveralls.io/builds/17741480)  
  
Coverage remained the same at 98.812% when pulling **c8993a92193310468e2c506be8f39319d9dbcfdf on jbuonagurio:develop** into **8f611f8c0e6a954b477d570f1eaa573be2004b20 on HDembinski:develop**.

---

## Comment 2

> Username: HDembinski  
> Created_at: 2018-06-29 11:28:53 UTC  
> Url: https://github.com/boostorg/histogram/pull/48#issuecomment-401327265  

Excellent, thank you! In the long-term, the pragmas should be replaced by macros which just don't do anything when the compiler does not support it. This makes the code more readable.

---
