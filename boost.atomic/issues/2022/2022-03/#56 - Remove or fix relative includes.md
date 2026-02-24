# #56 - Remove or fix relative includes [Closed]

> Username: Flamefire  
> Created at: 2022-03-06 11:56:35 UTC  
> Updated at: 2022-03-06 22:23:46 UTC  
> Closed at: 2022-03-06 22:21:43 UTC  
> Url: https://github.com/boostorg/atomic/issues/56  

On some systems relative includes are not automatically possible, i.e. the path to the current source file may not automatically added to the include-path.  
  
Currently Boost.Atomic fails on Cygwin 3.1.7 with:   
  
```  
libs\atomic\src\find_address_sse2.cpp:25:10: fatal error: find_address.hpp: No such file or directory  
   25 | #include "find_address.hpp"  
      |          ^~~~~~~~~~~~~~~~~~  
compilation terminated.  
libs\atomic\src\find_address_sse41.cpp:25:10: fatal error: find_address.hpp: No such file or directory  
   25 | #include "find_address.hpp"  
      |          ^~~~~~~~~~~~~~~~~~  
compilation terminated.  
```  
  
According to https://www.appveyor.com/docs/windows-images-software/#mingw-msys-cygwin the Appveyor images 2015&2017 use Cygwin 3.0.7 while the newer 2019&2022 use 3.1.7 where those failures appear. Hence I'd suggest to add CI for both.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-03-06 22:23:46 UTC  
> Url: https://github.com/boostorg/atomic/issues/56#issuecomment-1060050994  

Fixed, thanks.
