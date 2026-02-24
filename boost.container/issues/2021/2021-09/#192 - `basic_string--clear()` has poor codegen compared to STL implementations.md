# #192 - `basic_string::clear()` has poor codegen compared to STL implementations [Closed]

> Username: cmazakas  
> Created at: 2021-09-08 22:32:51 UTC  
> Updated at: 2021-09-13 12:20:00 UTC  
> Closed at: 2021-09-13 12:20:00 UTC  
> Url: https://github.com/boostorg/container/issues/192  

Consider the following snippets of code: https://godbolt.org/z/nEdj6hzoa  
  
Boost.Container's implementation seems to generate poor instructions in comparison to libstdc++.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-09-09 00:53:48 UTC  
> Updated at: 2021-09-09 00:56:51 UTC  
> Url: https://github.com/boostorg/container/issues/192#issuecomment-915674740  

The current implementation of `clear()`  
https://github.com/boostorg/container/blob/bfbab6ed7f641f62ba53093a9ae205970039e2b4/include/boost/container/string.hpp#L1951-L1962  
computes unnecessarily the size in `empty()` and repeatedly checks `is_short()` because of (I assume) aliasing issues when storing `char`s.  
  
Doing the `is_short` check once, and eliminating the `empty` check, in effect doing something like `if( is_short() ) priv_short_clear(); else priv_long_clear();` gives a much better codegen: https://godbolt.org/z/s1WG7rMr3 (gcc) https://godbolt.org/z/6nKrvWT1r (clang).

---

## Comment 2

> Username: igaztanaga  
> Created at: 2021-09-13 12:19:32 UTC  
> Url: https://github.com/boostorg/container/issues/192#issuecomment-918135249  

Many thanks for the report and many thanks Peter for the fix, I'll apply it as suggested. Reviewing other implementations, they also check is_short() and perform specialised steps in each case.
