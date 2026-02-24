# #172 Allow building without exceptions and add user-friendly diagnostics for unsupported compilers [Closed]

> Username: ldionne  
> Created at: 2015-09-03 22:42:44 UTC  
> Updated at: 2015-09-08 12:26:55 UTC  
> Closed at: 2015-09-08 12:26:55 UTC  
> Url: https://github.com/boostorg/hana/pull/172  

As discussed privately with @m-j-w, #168 will be replaced by this PR, which does not include support for non-fully-C++14 standard libraries.  
  
Also, instead of working around the presence of exceptions in the unit tests, we simply remove those exceptions and add a Travis configuration to build without exceptions altogether.  
  
Still left to sort out:  
- [x] Provide a sensible diagnostic when the standard library does not provide full support for C++14. This is quite hard to do reliably, because libc++ and libstdc++ do not include meaningful version numbers AFAICT.

---

## Comment 1

> Username: ldionne  
> Created_at: 2015-09-04 19:49:38 UTC  
> Url: https://github.com/boostorg/hana/pull/172#issuecomment-137837463  

@m-j-w, could you please confirm that   
1. No exception errors occur on Cygwin anymore  
2. You get a proper warning from `boost/hana/config.hpp` when trying to build with Clang 3.5 on Cygwin, because it uses an outdated libstdc++  
  
In principle, the only issues remaining when building on Cygwin with Clang 3.5.0 should be  
1. Missing `std::is_trivially_xxx` type traits  
2. Missing `std::to_string`, which fails a couple of examples  
  
Regarding those two, I decided not to even attempt workarounds. Both of these are C++11 features, not even C++14, so the failing stdlib must be quite outdated, right?

---
