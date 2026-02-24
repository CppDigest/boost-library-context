# #964 - Excesive test compile resources. [Closed]

> Username: grafikrobot  
> Created at: 2023-03-06 15:33:12 UTC  
> Updated at: 2023-03-07 19:09:11 UTC  
> Closed at: 2023-03-07 19:09:11 UTC  
> Url: https://github.com/boostorg/math/issues/964  

While working on modular B2 Boost changes I've been having to run all the library test (and examples, etc) builds. Building the Math tests fully is essentially impossible on reasonable hardware. Multiple times running the tests causes my system, when using parallel compiling, to run out of RAM and use all CPU time available. There are some *single* tests that cause GCC to use up anywhere from 33% to 99% of RAM (yes just one cc1plus process). This has caused such runs to halt, die, and kill the whole parent process chain.  
  
Please fix the tests so as to reduce the burden on everyone who runs testing (such as the expensive cloud CI providers).

---

## Comment 1

> Username: mborland  
> Created at: 2023-03-06 17:37:24 UTC  
> Url: https://github.com/boostorg/math/issues/964#issuecomment-1456602836  

Largest offending test suite should be mitigated by https://github.com/boostorg/math/pull/965
