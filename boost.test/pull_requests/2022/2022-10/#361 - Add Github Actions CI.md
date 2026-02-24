# #361 Add Github Actions CI [Closed]

> Username: Flamefire  
> Created at: 2022-10-10 13:32:37 UTC  
> Updated at: 2023-01-31 16:48:56 UTC  
> Closed at: 2023-01-31 16:02:26 UTC  
> Url: https://github.com/boostorg/test/pull/361  

Add CI via Github actions and Boost.CI  
  
Closes #332   
Closes #303  
  
Currently it fails on Windows due to a timeout in test-timeout-suite.cpp (I guess running 6 tests with a sleep of 100ms already gets over 1s) and on OSX where the custom RTTI implementation fails.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-01-31 16:02:25 UTC  
> Url: https://github.com/boostorg/test/pull/361#issuecomment-1410648107  

Superseded by #370

---
