# #653 - `compileflags=...` from command line doesn't work [Closed]

> Username: pdimov  
> Created at: 2020-09-06 00:27:24 UTC  
> Updated at: 2020-09-12 14:13:54 UTC  
> Closed at: 2020-09-12 14:13:54 UTC  
> Url: https://github.com/boostorg/build/issues/653  

```  
C:/boost-git/develop/tools/build/src/build\feature.jam:327: in validate-feature from module feature  
error: unknown feature "<compileflags>"  
```  
  
And indeed, `tools/features` has `cflags` and `cxxflags`, but not `compileflags`.
