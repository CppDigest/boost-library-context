# #128 - Why does it expand to BOOST_VERSION_NUMBER_AVAILABLE? [Open]

> Username: vinipsmaker  
> Created at: 2023-03-25 11:59:27 UTC  
> Updated at: 2023-03-26 11:04:47 UTC  
> Url: https://github.com/boostorg/predef/issues/128  

Boost.Predef macros (e.g. `BOOST_OS_WINDOWS`) expand to `BOOST_VERSION_NUMBER_AVAILABLE` instead of `1` or `0`. Why is that?  
  
By expanding to `BOOST_VERSION_NUMBER_AVAILABLE` I can't use these macros in Boost.PP. For instance, I recently had to use code like:  
  
```c  
BOOST_PP_IF(  
    BOOST_OS_UNIX,  
    lua_pushcfunction(L, tcp_socket_assign),  
    lua_pushcfunction(L, throw_enosys));  
```  
  
But it failed to compile because `BOOST_OS_UNIX` doesn't expand to `1` or `0`. Instead, it expands to a complex expression that can't be used in preprocessor metaprogramming.  
  
Could we offer `BOOST_OS_UNIX10` (and the equivalent to other macros as well) that expand to 1 or 0 instead of a complex expression? Or, alternatively, modify these macros to expand to `1` or `0` already?

---

## Comment 1

> Username: grafikrobot  
> Created at: 2023-03-25 14:02:31 UTC  
> Url: https://github.com/boostorg/predef/issues/128#issuecomment-1483832687  

Does using `BOOST_OS_UNIX()` or `BOOST_PP_EXPAND(BOOST_OS_UNIX)` work?

---

## Comment 2

> Username: vinipsmaker  
> Created at: 2023-03-26 11:02:19 UTC  
> Updated at: 2023-03-26 11:04:47 UTC  
> Url: https://github.com/boostorg/predef/issues/128#issuecomment-1484061605  

> Does using `BOOST_OS_UNIX()` or `BOOST_PP_EXPAND(BOOST_OS_UNIX)` work?  
  
No.  
  
The output for `BOOST_PP_IF(BOOST_PP_EXPAND(BOOST_OS_UNIX), a, b)` is:  
  
```  
BOOST_PP_IIF_BOOST_PP_BOOL_BOOST_PP_EXPAND(( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) ))(a, b)  
```  
  
If `BOOST_PP_IF(BOOST_PP_EXPAND(BOOST_OS_UNIX), a, b)` is given as input.
