# #139 Fix linking with ICU libs when building with clang-cl [Merged]

> Username: volo-zyko  
> Created at: 2021-06-09 18:59:16 UTC  
> Updated at: 2021-06-12 13:22:22 UTC  
> Merged at: 2021-06-12 13:22:22 UTC  
> Closed at: 2021-06-12 13:22:22 UTC  
> Url: https://github.com/boostorg/regex/pull/139  

When linking with ICU at least on clang-cl setup (not sure about cl) `advapi32.lib` is needed.

---
