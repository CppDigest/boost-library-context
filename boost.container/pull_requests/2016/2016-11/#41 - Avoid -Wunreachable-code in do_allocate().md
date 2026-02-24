# #41 Avoid -Wunreachable-code in do_allocate() [Merged]

> Username: mknejp  
> Created at: 2016-11-02 12:46:59 UTC  
> Updated at: 2016-11-12 23:24:54 UTC  
> Merged at: 2016-11-12 23:24:36 UTC  
> Closed at: 2016-11-12 23:24:36 UTC  
> Url: https://github.com/boostorg/container/pull/41  

do_allocate() triggered the warning  
"code will never be executed [-Wunreachable-code]"  
in Clang.  
  
Changed both do_allocate and do_deallocate to keep the existing  
similarity.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-11-12 23:24:54 UTC  
> Url: https://github.com/boostorg/container/pull/41#issuecomment-260155397  

Thanks for the report.

---
