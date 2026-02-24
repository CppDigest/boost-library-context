# #24 collector: Initialize `default_` before referencing it [Merged]

> Username: Flamefire  
> Created at: 2024-02-13 07:48:11 UTC  
> Updated at: 2024-02-14 16:01:32 UTC  
> Merged at: 2024-02-13 17:40:02 UTC  
> Closed at: 2024-02-13 17:40:02 UTC  
> Url: https://github.com/boostorg/quickbook/pull/24  

`quickbook::collector::collector()` causes a warning:  
> warning: member 'quickbook::collector::default_' is used uninitialized [-Wuninitialized]  
>   33 |     collector::collector() : main(default_), top(default_) {}  
  
Fix by moving it before `main`&`top` such that it is at least constructed before being referenced (main/top are reference_wrappers, so no actual use is done)

---
