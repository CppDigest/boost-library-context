# #45 - Unable to compile with clang 9 with enabled modules [Closed]

> Username: faserg1  
> Created at: 2020-08-14 09:00:16 UTC  
> Updated at: 2021-06-11 19:33:46 UTC  
> Closed at: 2020-08-20 12:32:58 UTC  
> Url: https://github.com/boostorg/dll/issues/45  

So the trouble is here:  
```  
.../vcpkg_installed/x64-linux/include/boost/dll/import.hpp:132:30: error: expected unqualified-id  
BOOST_DLL_IMPORT_RESULT_TYPE import(const boost::dll::fs::path& lib, const char* name,  
                             ^  
.../vcpkg_installed/x64-linux/include/boost/dll/import.hpp:146:18: fatal error: 'T' file not found  
    return import<T>(lib, name.c_str(), mode);  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-08-20 12:33:52 UTC  
> Url: https://github.com/boostorg/dll/issues/45#issuecomment-677637346  

This is going to be a breaking change, but there's no choice. Renamed `import` into `import symbol`.  
  
Many thanks for the report!

---

## Comment 2

> Username: vinipsmaker  
> Created at: 2021-06-11 19:33:46 UTC  
> Url: https://github.com/boostorg/dll/issues/45#issuecomment-859796708  

> This is going to be a breaking change, but there's no choice.  
  
It's understandable.  
  
Anyway it'd be nice to at least mention this breaking change at: https://www.boost.org/doc/libs/1_76_0/doc/html/boost_dll/revision_history.html  
  
I've had to go through the commit history to understand what happened.
