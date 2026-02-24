# #269 - boost::parser compilation error on mac with boost 1.88 [Open]

> Username: SilkeSchomann  
> Created at: 2025-08-12 08:27:41 UTC  
> Updated at: 2025-12-19 03:11:46 UTC  
> Url: https://github.com/boostorg/parser/issues/269  

view_adaptor.hpp (boost::parser) does not compile on a mac with clang 18.1.8 and boost 1.88:  
  
```  
/include/boost/parser/detail/stl_interfaces/view_adaptor.hpp:199:35: error: no template named 'range_adaptor_closure' in namespace 'std::ranges'; did you mean 'std::__range_adaptor_closure'?  
199 |     using range_adaptor_closure = std::ranges::range_adaptor_closure<D>;  
    |                                                          ^~~~~~~~~~~~~  
/bin/../include/c++/v1/__ranges/range_adaptor.h:58:8: note: 'std::__range_adaptor_closure' declared here  
58 | struct __range_adaptor_closure {  
   |            ^  
1 error generated.  
```

---

## Comment 1

> Username: mclow  
> Created at: 2025-08-12 13:33:33 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3179366434  

Can you try this with the Boost 1.89 release candidate, please?  
  
https://archives.boost.io/release/1.89.0/source/

---

## Comment 2

> Username: SilkeSchomann  
> Created at: 2025-08-14 08:19:42 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3187441406  

@mclow With the Boost 1.89 release candidate, I am getting exactly the same error message as with version 1.88.

---

## Comment 3

> Username: jwwalker  
> Created at: 2025-09-02 02:24:02 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3243587145  

What version of the C++ standard are you using?  As far as I can tell with cppreference.com, range_adaptor_closure is a C++23 feature.

---

## Comment 4

> Username: SilkeSchomann  
> Created at: 2025-09-10 12:32:23 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3274756525  

We are using C++ 20 at the moment.

---

## Comment 5

> Username: jwwalker  
> Created at: 2025-09-10 16:21:38 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3275657043  

The line where you got a compile error is subject to `#if BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE`.  Earlier in the same file, that macro is defined as follows:  
  
```  
#if BOOST_PARSER_USE_CONCEPTS && defined(__cpp_lib_ranges) &&                  \  
    202202L <= __cpp_lib_ranges  
#define BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE 1  
#else  
#define BOOST_PARSER_USE_CPP23_STD_RANGE_ADAPTOR_CLOSURE 0  
#endif  
```  
  
What is the value of `__cpp_lib_ranges` for you?  I take it you are not using Xcode and Apple Clang.  Maybe there is some mismatch between the compiler dialect and the version of the standard library you are using.

---

## Comment 6

> Username: tzlaine  
> Created at: 2025-10-13 01:33:17 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3395603049  

There is.  Your stdlib is lying to you.  Unfortunately, it's pretty hard to fix this without knowing which versions of clang actually have range_adaptor_closure, and which ones do not.  I certainly don't know. I guess I need to spend some quality time with Compiler Explorer.

---

## Comment 7

> Username: tzlaine  
> Created at: 2025-10-13 23:03:39 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3399346447  

I cannot repro this on Godbolt with Clang 18.1 (or Clang 17).  https://godbolt.org/z/3jxEfhfhz  
  
If you can give me a Godbolt repro, I can try and work around the affected compiler version(s).  
  
As of now, I have no way to fix this.

---

## Comment 8

> Username: progheal  
> Created at: 2025-12-19 03:11:05 UTC  
> Updated at: 2025-12-19 03:11:46 UTC  
> Url: https://github.com/boostorg/parser/issues/269#issuecomment-3673304075  

> Your stdlib is lying to you.  
  
As this comment pointed out, the problem is not the version of *clang*, rather the version of *libc++*. IIRC Clang does not come with a pre-coupled libc++ version, one almost always *separately* install a version of libc++.  
  
I found this issue when I encountered this exact problem when compiling from boost 1.89. I then checked my libc++ version using [this method](https://stackoverflow.com/a/38445708), which returns something like `#define _LIBCPP_VERSION 180000`, presumably some version 18. I then checked my package manager and found there is an upgrade to libc++ (according to it, it is "version 27c" upgrading to "version 29"), upgraded it, and the problem went away. The same version check now returns `#define _LIBCPP_VERSION 210000`, presumably some version 21.  
  
TL;DR: try upgrade **libc++**.
