# #692 Add missing case for <architecture>x86/<address-model> [Merged]

> Username: pdimov  
> Created at: 2020-12-28 05:17:18 UTC  
> Updated at: 2021-10-02 20:59:43 UTC  
> Merged at: 2020-12-28 11:26:13 UTC  
> Closed at: 2020-12-28 11:26:13 UTC  
> Url: https://github.com/boostorg/build/pull/692  

This fixes the config check for the default address model which otherwise resulted in  
```  
compile-c-c++ ..\..\bin.v2\libs\config\checks\architecture\clang-win-11.0.0\debug\threading-multi\32.obj  
      "..\..\libs\config\checks\architecture\32.cpp" -Fo"..\..\bin.v2\libs\config\checks\architecture\clang-win-11.0.0\debug\threading-multi\32.obj"    -TP /EHs /GR /Z7 /Od /Ob0 /W3 /MDd -c -DBOOST_ALL_NO_LIB=1 "-I..\.."   
```  
and launching of `32.cpp` in the default editor.

---

## Comment 1

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:43 UTC  
> Url: https://github.com/boostorg/build/pull/692#issuecomment-932819686  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
