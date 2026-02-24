# #365 Fix search path for relative includes [Closed]

> Username: Flamefire  
> Created at: 2022-03-06 16:25:49 UTC  
> Updated at: 2023-09-12 07:20:28 UTC  
> Closed at: 2023-09-12 07:19:48 UTC  
> Url: https://github.com/boostorg/thread/pull/365  

On recent Cygwins and other systems the source files path may not be added to the include search path leading to:  
> libs\thread\src\pthread\once.cpp:8:10: fatal error: ./once_atomic.cpp: No such file or directory  
>    8 | #include "./once_atomic.cpp"  
  
Add the path explicitly and add CI jobs for this (order/layout based on Boost.CI)  
  
Required also for downstream libraries, see e.g. https://github.com/boostorg/atomic/pull/57

---

## Comment 1

> Username: Flamefire  
> Created_at: 2022-03-07 11:05:24 UTC  
> Updated_at: 2022-03-07 11:06:15 UTC  
> Url: https://github.com/boostorg/thread/pull/365#issuecomment-1060529257  

@pdimov Seemingly this is not as trivial as thought as the tests heavily rely on relative includes. Sometimes in the form of `#include "../../../remove_error_code_unused_warning.hpp"` which can't be easily fixed with an `<include>.`.  
  
So my proposal: Copy all headers used by tests (in the `test` folder or one of its subfolders) to `test/include` and add that folder to the include search path. Then adapt the include directives to `#include "remove_error_code_unused_warning.hpp"`  
  
Any other idea?  
  
BTW: No idea why the MSVC tests start to fail. Probably a time-related flake.

---

## Comment 2

> Username: Flamefire  
> Created_at: 2023-09-12 07:20:26 UTC  
> Url: https://github.com/boostorg/thread/pull/365#issuecomment-1715141921  

Turned out this was a B2 bug with slashes confusing the compiler -> Close

---
