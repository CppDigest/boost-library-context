# #130 #123: Fixes for calling convention collision errors when compiling with /clr [Merged]

> Username: millerrt3  
> Created at: 2019-07-16 21:11:28 UTC  
> Updated at: 2021-03-08 11:40:43 UTC  
> Merged at: 2021-03-08 11:40:42 UTC  
> Closed at: 2021-03-08 11:40:43 UTC  
> Url: https://github.com/boostorg/type_traits/pull/130  

- Changed `__CLR_VER` to `_MANAGED` in accordance with [Microsoft's guidance on `/clr` detection](https://docs.microsoft.com/en-us/cpp/dotnet/how-to-detect-clr-compilation?view=vs-2019)  
- Modified preprocessor conditional checks to more explicitly control the effect on `/clr` compilation on enabled calling conventions (`__clrcall` will be the enabled if and only if `_MANAGED` is set; standard logic will be used in all other cases)  
  
I've tested these changes using MSVC to build both 32- and 64-bit projects with and without the `/clr` flag enabled.

---

## Comment 1

> Username: millerrt3  
> Created_at: 2019-07-19 13:14:56 UTC  
> Updated_at: 2019-07-22 13:30:54 UTC  
> Url: https://github.com/boostorg/type_traits/pull/130#issuecomment-513223916  

AppVeyor failures appear to be caused by removing definitions for `__stdcall` and `__cdecl` conventions when compiling with `/clr` enabled. I don't think I'll have a chance to look into this today, but I'll do some deeper investigation and amend the PR with a fix within the next few days.

---

## Comment 2

> Username: millerrt3  
> Created_at: 2019-07-22 13:30:40 UTC  
> Updated_at: 2019-07-22 13:31:11 UTC  
> Url: https://github.com/boostorg/type_traits/pull/130#issuecomment-513792041  

I'm not well-versed enough in the interactions between `/clr` compilation and calling conventions to say for sure, but some of the text on Microsoft's [reference page for `__clrcall`](https://docs.microsoft.com/en-us/cpp/cpp/clrcall?view=vs-2019) seems to imply that `__clrcall` may be the only valid convention to use in that context:  
  
> /clr (Common Language Runtime Compilation) implies that all functions and function pointers are __clrcall and the compiler will not permit a function inside the compiland to be marked anything other than __clrcall.  
  
By my understanding, that would imply that the `__cdecl` and `__stdcall` overloads that the test suite is expecting may not be valid (or necessary), which would imply that the resolution to the failed tests might simply be to remove them. I'm hesitant to remove functionality unless I'm positive that it needs to be removed, though, so I plan to do some more investigation before amending the PR.

---

## Comment 3

> Username: millerrt3  
> Created_at: 2019-07-22 15:52:06 UTC  
> Url: https://github.com/boostorg/type_traits/pull/130#issuecomment-513847194  

Further reading implies that `__stdcall` and `__cdecl` are indeed (contextually) valid for `/clr` compilations (some other documentation, such as for [error C3642](https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-2/compiler-error-c3642) would be nonsensical if `__clrcall` functions couldn't exist in projects alongside other calling conventions). It appears that the documentation referenced above may instead have been referring to the `/clr:pure` and `/clr:safe` flags (documented on the main [`/clr` reference page](https://docs.microsoft.com/en-us/cpp/build/reference/clr-common-language-runtime-compilation)). These flags were deprecated in Visual Studio 2015 and removed in Visual Studio 2017; for improved compatibility with older versions, they can be detected (according to Microsoft's [predefined macros documentation](https://docs.microsoft.com/en-us/cpp/preprocessor/predefined-macros)) by the presence of `_M_CEE_SAFE` or `_M_CEE_PURE`.  
  
The failing tests are therefore correct: `__stdcall` and `__cdecl` should indeed be valid for most contexts of `/clr` compilation. I'll revise my implementation accordingly.

---

## Comment 4

> Username: millerrt3  
> Created_at: 2019-09-30 13:21:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/130#issuecomment-536557809  

Following up on this PR--is there any indication of when this might be able to get merged (or, if not these exact changes, some other fix for boostorg#123)?

---
