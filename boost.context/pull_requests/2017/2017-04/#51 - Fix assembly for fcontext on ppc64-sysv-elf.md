# #51 Fix assembly for fcontext on ppc64/sysv/elf [Merged]

> Username: DaoWen  
> Created at: 2017-04-05 02:38:09 UTC  
> Updated at: 2017-04-05 04:38:55 UTC  
> Merged at: 2017-04-05 04:38:55 UTC  
> Closed at: 2017-04-05 04:38:55 UTC  
> Url: https://github.com/boostorg/context/pull/51  

Fixes bugs in the `fcontext` assembly functions for the ppc64/sysv/elf platform.  
  
See issue #50 for more details.  
  
This patch was tested against the latest `develop` commit (a429ff6301) using GCC 5.4 on a POWER7 machine running RHEL 6.6. However, I am not at all familiar with the Boost built/test system, so I am not entirely confident that I ran the unit tests correctly. I ran the following command from the Boost project root directory:  
  
    ./b2 cxxflags="-std=c++11" --build-dir="/tmp/nick/boost/build" --with-context libs/context/test  
  
The tests ran and failed on the first test without the patch, and then several tests ran and all passed after applying the patch. I think that means the patch works correctly (as far as the functionality covered by those tests goes anyway). Note that since my platform only uses ELFv1, I was not able to verify the ELFv2-specific code.

---

## Review 1 [Commented]

> Username: DaoWen  
> Created_at: 2017-04-05 02:41:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/context/pull/51#pullrequestreview-30946831  

📁 test/test_fcontext.cpp

```diff
  10 | #include <string>
  11 | #include <utility>
  12 |+ #include <cstdio>
```

> Username: DaoWen  
> Created_at: 2017-04-05 02:41:55 UTC  
> Updated_at: 2017-04-05 02:43:29 UTC  
> Url: https://github.com/boostorg/context/pull/51#discussion_r109820724  

I get an error without this include: *`sscanf` is not a member of namespace `std`*


---

## Comment 2

> Username: olk  
> Created_at: 2017-04-05 04:38:38 UTC  
> Url: https://github.com/boostorg/context/pull/51#issuecomment-291752121  

ty

---
