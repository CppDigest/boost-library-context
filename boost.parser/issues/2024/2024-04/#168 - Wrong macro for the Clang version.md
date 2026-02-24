# #168 - Wrong macro for the Clang version [Closed]

> Username: intractabilis  
> Created at: 2024-04-24 18:22:53 UTC  
> Updated at: 2024-09-29 20:43:13 UTC  
> Closed at: 2024-09-29 19:51:17 UTC  
> Url: https://github.com/boostorg/parser/issues/168  

https://github.com/tzlaine/parser/blob/4cea9c03d6baf8165a21162e66be4f99ec85b529/include/boost/parser/config.hpp#L83  
`__clang__` above is not the Clang version; it's equal to 1.  
```  
$ clang++ --version  
clang version 18.1.4 (https://github.com/llvm/llvm-project.git e6c3289804a67ea0bb6a86fadbe454dd93b8d855)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
$ clang++ -x c++ -E -dD - < /dev/null 2>&1 | grep __clang__  
#define __clang__ 1  
```  
You probably meant `__clang_major__`:  
```  
$ clang++ -x c++ -E -dD - < /dev/null 2>&1 | grep __clang_major__  
#define __clang_major__ 18  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-29 20:43:12 UTC  
> Url: https://github.com/boostorg/parser/issues/168#issuecomment-2381596042  

Thanks for reporting this!
