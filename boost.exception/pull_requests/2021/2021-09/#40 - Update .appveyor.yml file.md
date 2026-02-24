# #40 Update .appveyor.yml file [Closed]

> Username: sdarwin  
> Created at: 2021-09-30 20:36:00 UTC  
> Updated at: 2022-09-01 14:28:40 UTC  
> Closed at: 2022-09-01 14:28:40 UTC  
> Url: https://github.com/boostorg/exception/pull/40  

The current appveyor build shows one failure.   In an attempt to correct that, copied in the generic .appveyor.yml file from boost-ci.   Everything is building except "FLAVOR: cygwin (64-bit)", which has been commented out.   Please test that further when you have a chance. Looking at the cygwin job that had been running before, it says:  
  
```  
- default address-model    : 32-bit [1]  
```  
Therefore, it could be that "FLAVOR: cygwin (64-bit)" hasn't been tested.

---
