# #1201 - Review 2: <format> support [Closed]

> Username: jzmaddock  
> Created at: 2025-11-04 18:53:39 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-06 19:19:54 UTC  
> Url: https://github.com/boostorg/decimal/issues/1201  

>The checks for whether or not the <format> header is available checks compiler versions. However, it is feasible that people are using a different standard library from the one you are assuming based on the compiler version (for example clang compilation may or may not have the -stdlib=libc++ flag, or someone might be using a modern clang with an older GNU libstdc++ lacking <format>).  
It would perhaps be more reliable to check for the __cpp_lib_format feature test macro that C++20 provides.  
  
We have BOOST_NO_CXX20_HDR_FORMAT for this, otherwise the correct check is something like:  
  
```  
#if (!__has_include(<format>) || !defined(__cpp_lib_format) || (__cpp_lib_format < 201907L))  
// not supported  
#endif  
```

---

## Comment 1

> Username: mborland  
> Created at: 2025-11-05 10:08:08 UTC  
> Url: https://github.com/boostorg/decimal/issues/1201#issuecomment-3490312937  

Yes this probably works now. When format support was initially being added checking `__cpp_lib_format` didn't always lead to a working STL implementation, or it was prior to the value being defined. The situation is much better now.
