# #120 - Local Variable is Initialized but Not Referenced [Closed]

> Username: jwillikers  
> Created at: 2020-02-10 19:50:43 UTC  
> Updated at: 2020-03-17 12:50:07 UTC  
> Closed at: 2020-03-17 12:50:07 UTC  
> Url: https://github.com/boostorg/process/issues/120  

I encounter this warning compiling an application that uses Boost process 1.72.0 with MSVC.  
```cpp  
include\boost\process\detail\windows\handles.hpp(37): warning C4189: 'cnt': local variable is initialized but not referenced  
```

---

## Comment 1

> Username: jonesmz  
> Created at: 2020-03-13 06:27:55 UTC  
> Url: https://github.com/boostorg/process/issues/120#issuecomment-598571640  

There is no 'cnt' variable in handls.hpp

---

## Comment 2

> Username: tt4g  
> Created at: 2020-03-13 07:11:39 UTC  
> Updated at: 2020-03-13 07:11:55 UTC  
> Url: https://github.com/boostorg/process/issues/120#issuecomment-598583268  

Already fixed in #97 but not released.
