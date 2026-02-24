# #385 Add BOOST_CLANG_VERSION macro [Merged]

> Username: pdimov  
> Created at: 2021-07-04 16:17:56 UTC  
> Updated at: 2021-07-06 11:48:32 UTC  
> Merged at: 2021-07-06 11:48:31 UTC  
> Closed at: 2021-07-06 11:48:31 UTC  
> Url: https://github.com/boostorg/config/pull/385  

This defines `BOOST_CLANG_VERSION` when the compiler is Clang. This is usually `__clang_major__ * 10000 + __clang_minor__ * 100 + __clang_patchlevel__`, analogous to the `BOOST_GCC` encoding scheme. On Apple Clang, the reported version (which is set by Apple and doesn't correspond to the "real" version) is translated to the "right" one based on the list in https://en.wikipedia.org/wiki/Xcode#Toolchain_versions.  
  
It would have been more consistent to set the existing `BOOST_CLANG` macro to this value, like all the other compiler macros work; however, `BOOST_CLANG` is already documented to be exactly `1`, and there is already code that compares it to `1`.

---
