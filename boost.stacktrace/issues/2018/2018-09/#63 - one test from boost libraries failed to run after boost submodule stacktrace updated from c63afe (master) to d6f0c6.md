# #63 - one test from boost libraries failed to run after boost submodule stacktrace updated from c63afe (master) to d6f0c6 [Closed]

> Username: spacelg  
> Created at: 2018-09-21 01:50:06 UTC  
> Updated at: 2018-09-22 18:44:42 UTC  
> Closed at: 2018-09-22 18:44:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/63  

Environment:  
 VS 2017 + Windows Server 2016  
  
Issue description:  
 We build and run test for boost. And we found a tests failed to run after boost submodule stacktrace updated from c63afe (master) to d6f0c6 ([boostorg/boost@4ca9fa2](https://github.com/boostorg/boost/commit/4ca9fa2136e75b125a2009b1a4ced33b856fbe9c)). It seems the test failed due to "Performing configuration checks". Could you please take a look?   
  
ErrorMessage:  
[executing command] .\b2 -j4 variant=release cxxflags="/Oy-" --build-dir=..\out\x86rel libs\stacktrace\test   
Performing configuration checks  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
D:/Boost/src/tools/build/src/build\feature.jam:327: in validate-feature from module feature  
error: unknown feature "<visibility>"  
  
[log_x86_test_100.log](https://github.com/boostorg/stacktrace/files/2403610/log_x86_test_100.log)

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-09-21 05:53:17 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/63#issuecomment-423421484  

That's because it relies on a feature https://github.com/boostorg/build/pull/331 that was not merged to master yet.

---

## Comment 2

> Username: apolukhin  
> Created at: 2018-09-22 18:44:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/63#issuecomment-423764895  

Should build well now.   
Thanks for the bug report!
