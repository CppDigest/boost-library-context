# #190 Make updates for CMake version, dependencies, and C++ standard [Merged]

> Username: jefftrull  
> Created at: 2023-10-23 22:07:45 UTC  
> Updated at: 2023-10-25 22:32:25 UTC  
> Merged at: 2023-10-25 22:32:25 UTC  
> Closed at: 2023-10-25 22:32:25 UTC  
> Url: https://github.com/boostorg/wave/pull/190  

These issues were noted by @pdimov and the fix was to rerun boostdep --cmake (with a couple of manual changes for src/cpplexer files).  
  
If merged this will resolve #189

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-10-23 23:01:36 UTC  
> Url: https://github.com/boostorg/wave/pull/190#issuecomment-1776158570  

It's OK, but why reorder the source files? It just makes the diff more complicated for no good reason.

---

## Comment 2

> Username: jefftrull  
> Created_at: 2023-10-23 23:52:14 UTC  
> Url: https://github.com/boostorg/wave/pull/190#issuecomment-1776233908  

My thinking is that boostdep is now producing them in a different order, so by taking the reordering now we will have shorter diffs in the future.  
  
I guess I could just always reorder them to match going forward instead. Hm... What do you think?

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-10-23 23:56:37 UTC  
> Url: https://github.com/boostorg/wave/pull/190#issuecomment-1776237523  

The order in which the files are enumerated might be OS-dependent; on Windows, `boostdep` produces the same sorted list, but you are probably on Linux or Mac. Well, not that big of a deal, I suppose.

---
