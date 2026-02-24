# #542 - b2 does not build using MSVC with Unicode support (/DUNICODE) [Closed]

> Username: mloskot  
> Created at: 2020-02-27 19:32:04 UTC  
> Updated at: 2020-02-28 13:32:35 UTC  
> Closed at: 2020-02-28 13:32:35 UTC  
> Url: https://github.com/boostorg/build/issues/542  

The `b2` driver code implicitly assumes use of ANSI version of Win32 API functions, e.g.  
  
```  
char buf[ 1024 ];  
DWORD const ret = GetModuleFileName( NULL, buf, sizeof( buf ) );  
```  
  
Obviously, attempts to compile with `cl /DUNICODE` will fail due to `char*` to `wchar_t*` conversions as `GetModuleFileName` resolves to `GetModuleFileNameW`.  
  
There either should be an early exit somewhere  
  
```  
#if defined(UNICODE) || defined(_UNICODE)  
#error "Building with C run-time support for Unicode is not supported"  
#endif  
```  
  
or ANSI variants of Win32 API functions should be preferred, change from `GetModuleFileName` to `GetModuleFileNameA`, etc.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-02-27 19:36:07 UTC  
> Url: https://github.com/boostorg/build/issues/542#issuecomment-592139463  

I like being explicit, and hence we should use the ANSI variants. At least until some future when we can make b2 support unicode.
