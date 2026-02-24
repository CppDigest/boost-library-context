# #33 Remove boost_install call from CMakeLists.txt [Merged]

> Username: pdimov  
> Created at: 2020-06-04 12:13:58 UTC  
> Updated at: 2020-06-04 12:17:53 UTC  
> Merged at: 2020-06-04 12:17:38 UTC  
> Closed at: 2020-06-04 12:17:38 UTC  
> Url: https://github.com/boostorg/atomic/pull/33  

Predef has been added as a dependency and it doesn't have install support, which causes errors such as  
```  
CMake Error: install(EXPORT "boost_atomic-targets" ...) includes target "boost_atomic" which requires target "boost_predef" that is not in the export set.  
```  
because CMake is picky about these things.  
  
The superproject can add install support automatically nowadays (but only on CMake 3.13 or later), so an explicit `boost_install` call isn't necessary and the easiest way forward is just to not do it. I'll be gradually removing these `boost_install` calls from all other libraries as well.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-04 12:17:45 UTC  
> Url: https://github.com/boostorg/atomic/pull/33#issuecomment-638811623  

Thanks.

---
