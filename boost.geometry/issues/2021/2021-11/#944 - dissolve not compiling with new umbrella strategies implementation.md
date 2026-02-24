# #944 - dissolve not compiling with new umbrella strategies implementation [Closed]

> Username: n4z4m3  
> Created at: 2021-11-23 18:34:06 UTC  
> Updated at: 2022-01-19 23:36:45 UTC  
> Closed at: 2022-01-19 23:36:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/944  

Using the dissolve algorithm causes the compiler to fail.  
In Visual Studio 2019 with Windows 10, it gives the following error on dissolver.h  
  
Error C2065 'envelope_strategy_type': undeclared identifier  
  
Dissolve (and probably the other algorithms in the extensions) need to be updated to make use of the new umbrella strategies that are part of the latest versions of boost-geometry.

---

## Comment 1

> Username: awulkiew  
> Created at: 2022-01-18 16:55:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/944#issuecomment-1015615530  

Thanks!
