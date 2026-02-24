# #272 - Regression boost 1.87: offset_ptr conversion from `void` no longer works [Closed]

> Username: liss-h  
> Created at: 2025-09-29 09:59:00 UTC  
> Updated at: 2025-11-10 22:51:04 UTC  
> Closed at: 2025-11-10 22:51:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/272  

Hello,  
  
in boost <=1.86 the following always worked to convert a `offset_ptr<void>` to another type (e.g. `offset_ptr<int>`).  
  
```c++  
boost::interprocess::offset_ptr<void> x{};  
auto y = boost::interprocess::offset_ptr<int>{x};  
```  
  
But since 1.87 it no longer works (the constraints are no longer satisfied) see: https://godbolt.org/z/T36xrvMnn  
  
`reinterpret_pointer_cast<int>(x)` also does not work by default because it cannot infer the additional template params (difference type and such), so they would always need to be provided. I assume this is not intended.  
  
If it is intended, I would be glad if you would show me the intended way for the conversion.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-11-10 22:46:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/272#issuecomment-3514214257  

Conversion from "void" to another type was an unsafe loophole, just like you can't convert a "void *" to "int*". But the xxx_cast functions are not working at all (bug) and they are not tested. You should be able to use `reinterpret_pointer_cast<int>(x)`  or `static_pointer_cast<int>(x)`. I'll commit a fix ASAP.   
  
As a workaround, you can obtain the raw pointer using x.get(), do the `static_cast<int*>(x.get())` and convert the raw `int *` pointer again to a `offset_ptr<int>`.
