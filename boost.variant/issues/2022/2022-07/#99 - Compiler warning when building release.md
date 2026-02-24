# #99 - Compiler warning when building release [Closed]

> Username: orgads  
> Created at: 2022-07-25 12:42:31 UTC  
> Updated at: 2022-09-01 10:59:20 UTC  
> Closed at: 2022-09-01 10:59:20 UTC  
> Url: https://github.com/boostorg/variant/issues/99  

This is with GCC 4.1.2.  
  
```  
/usr/include/boost/variant/detail/forced_return.hpp: In function 'T boost::detail::variant::forced_return() [with T = bool]':  
/usr/include/boost/variant/detail/forced_return.hpp:41: warning: 'noreturn' function does return  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-01 10:59:20 UTC  
> Url: https://github.com/boostorg/variant/issues/99#issuecomment-1234110510  

Modern versions of GCC do not complain on that. Warnings for such outdated versions of compilers won't be fixed in the library, as it may (and would) add warnings on modern compilers.
