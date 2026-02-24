# #469 chore: remove meaningless comparison [Merged]

> Username: e-kwsm  
> Created at: 2025-01-22 02:28:55 UTC  
> Updated at: 2025-10-25 20:27:19 UTC  
> Merged at: 2025-10-25 19:23:44 UTC  
> Closed at: 2025-10-25 19:23:44 UTC  
> Url: https://github.com/boostorg/python/pull/469  

```console  
$ faber --builddir=build cxx.name=clang++  
…  
clangxx.compile src.object/function.o  
./src/object/function.cpp:164:42: warning: result of comparison of constant 9223372036854775807 with expression of type 'unsigned int' >  
  164 |                         assert(max_arity <= static_cast<std::size_t>(ssize_t_max));  
      |                                ~~~~~~~~~ ^  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/include/assert.h:100:27: note: expanded from macro 'assert'  
  100 |      (static_cast <bool> (expr)                                         \  
      |                           ^~~~  
1 warning generated.  
…」  
```

---
