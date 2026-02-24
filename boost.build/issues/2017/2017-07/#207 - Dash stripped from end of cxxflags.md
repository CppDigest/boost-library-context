# #207 - Dash stripped from end of cxxflags [Closed]

> Username: vinniefalco  
> Created at: 2017-07-03 16:13:23 UTC  
> Updated at: 2017-12-15 22:18:05 UTC  
> Closed at: 2017-12-15 22:18:05 UTC  
> Url: https://github.com/boostorg/build/issues/207  

Using  
```  
<toolset>msvc:<cxxflags>"/permissive-"  
```  
  
Causes Boost.Build to strip the trailing dash, passing just `"/permissive"` to cl.exe

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-07-03 16:26:50 UTC  
> Url: https://github.com/boostorg/build/issues/207#issuecomment-312688238  

That just seems wrong.. aka a bug.
