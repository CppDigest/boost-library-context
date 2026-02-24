# #34 - boost-exception build failure with msvc with both flags: "/std:c++17" and "/std:c17" [Closed]

> Username: playgithub  
> Created at: 2020-12-24 12:00:11 UTC  
> Updated at: 2020-12-26 05:00:27 UTC  
> Closed at: 2020-12-26 05:00:27 UTC  
> Url: https://github.com/boostorg/exception/issues/34  

error message  
```console  
cl : Command line error D8016 : '/std:c++17' and '/std:c17' command-line options are incompatible  
```  
  
https://github.com/microsoft/vcpkg/issues/15232

---

## Comment 1

> Username: playgithub  
> Created at: 2020-12-26 05:00:27 UTC  
> Url: https://github.com/boostorg/exception/issues/34#issuecomment-751318472  

Fixed  
commit: https://github.com/boostorg/build/commit/ab341585995464332733d2852a50f9696867e711
