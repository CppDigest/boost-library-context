# #204 - When <C++11 is used, build/configuration step should fail [Closed]

> Username: Warchant  
> Created at: 2019-05-16 22:30:14 UTC  
> Updated at: 2019-05-17 05:55:41 UTC  
> Closed at: 2019-05-17 05:55:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/204  

- Mac OS 10.14.4  
- Boost 1.70.0  
  
```  
1. git clone --recursive https://github.com/boostorg/boost  
2. cd boost  
3. ./bootstrap --with-libraries=chrono,thread,filesystem,system,thread,fiber  
4. ./b2  
```  
  
**Actual behaviour**: For some reason C++98 standard is used by default. Build *succeeds*, and `stage/libs` does not contain `libboost_fiber*.{a,dylib}`.   
  
**Expected behaviour**: build fails with message "you're using C++98, but minimal requirement for Boost.Fiber is C++11".

---

## Comment 1

> Username: olk  
> Created at: 2019-05-17 05:55:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/204#issuecomment-493329986  

If compiling one boost lib fails - why would you abort compiling the other boost libs?  
You have to address this to boost.build.
