# #302 - Warning/error about unreferenced parameter in context.cpp [Closed]

> Username: mojca  
> Created at: 2022-11-29 22:06:54 UTC  
> Updated at: 2022-11-30 10:44:35 UTC  
> Closed at: 2022-11-30 07:17:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/302  

When building the code on Windows with MSVC 2022 with "treat warnings as errors" I'm getting the following error:  
```  
C:\build\__external\src\boost\libs\fiber\src\context.cpp(33,36): error C2220: the following warning is treated as an error [C:\build\_deps\boost-build\libs\fiber\boost_fiber.vcxproj]  
C:\build\__external\src\boost\libs\fiber\src\context.cpp(33,36): warning C4100: 'c': unreferenced formal parameter [C:\build\_deps\boost-build\libs\fiber\boost_fiber.vcxproj]  
```  
https://github.com/boostorg/fiber/blob/3e1770607c88a7a67869bd50fdd70f6da7be19c0/src/context.cpp#L33-L39

---

## Comment 1

> Username: olk  
> Created at: 2022-11-30 07:17:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/302#issuecomment-1331734712  

fixed, ty

---

## Comment 2

> Username: mojca  
> Created at: 2022-11-30 10:44:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/302#issuecomment-1331957226  

Thank you very much.  
Would it be possible to include this into the 1.81 release?
