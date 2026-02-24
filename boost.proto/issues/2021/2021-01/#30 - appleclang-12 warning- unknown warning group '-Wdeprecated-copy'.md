# #30 - appleclang-12 warning: unknown warning group '-Wdeprecated-copy' [Open]

> Username: k15tfu  
> Created at: 2021-01-28 11:10:18 UTC  
> Updated at: 2021-04-29 22:30:47 UTC  
> Url: https://github.com/boostorg/proto/issues/30  

In file boost/proto/expr.hpp:  
```  
boost/proto/expr.hpp:140:44: error: unknown warning group '-Wdeprecated-copy', ignored [-Werror,-Wunknown-warning-option]  
            #pragma GCC diagnostic ignored "-Wdeprecated-copy"  
                                           ^  
1 error generated.  
```  
  
Related to #22, #24.

---

## Comment 1

> Username: peter-bloomfield  
> Created at: 2021-02-04 16:37:38 UTC  
> Updated at: 2021-02-04 16:37:58 UTC  
> Url: https://github.com/boostorg/proto/issues/30#issuecomment-773442486  

I was able to work around this problem by selectively disabling "-Wunknown-warning-option" in my code. For example:  
  
```  
#if defined(__clang__)  
    #pragma clang diagnostic push  
    #pragma clang diagnostic ignored "-Wunknown-warning-option"  
#endif  
  
#include <boost/log/expressions.hpp>  
      
#if defined(__clang__)  
    #pragma clang diagnostic pop  
#endif  
```

---

## Comment 2

> Username: acmorrow  
> Created at: 2021-04-29 22:30:46 UTC  
> Url: https://github.com/boostorg/proto/issues/30#issuecomment-829637415  

@ericniebler and @Kojoley - Any possibility of a fix for this issue? I would like to upgrade to boost 1.76 but am currently unable to do so because of this issue. It is unfortunate that Apple Xcode gets tripped up like this, but building with `-Wno-unknown-warning-option` isn't really possible in my situation, since it can affect the meaning of configure checks for the availability of warning flags:  
  
```  
clang++ -Werror -Wthing-that-doesnt-exist -c ./hello_world.cpp ; echo $?  
error: unknown warning option '-Wthing-that-doesnt-exist' [-Werror,-Wunknown-warning-option]  
1  
```  
  
```  
clang++ -Werror -Wthing-that-doesnt-exist -Wno-unknown-warning-option -c ./hello_world.cpp ; echo $?  
0  
```  
  
So I can't really feed that flag into the build at issue via `CFLAGS`  on the command line. And because the error comes from a header, I can't scope application of `-Wno-unknown-warning-option` just to the part of the build that compiles boost - code that consumes the `expr.hpp` header is also affected. I could do as @peter-bloomfield has done and wrap all uses of boost headers that transitively include `expr.hpp`, but that is somewhat unpalatable for a larger project.
