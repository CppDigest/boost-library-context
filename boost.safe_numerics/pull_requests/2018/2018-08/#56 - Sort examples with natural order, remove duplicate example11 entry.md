# #56 Sort examples with natural order, remove duplicate example11 entry [Closed]

> Username: janisozaur  
> Created at: 2018-08-01 21:02:24 UTC  
> Updated at: 2018-09-23 22:26:37 UTC  
> Closed at: 2018-08-14 22:13:12 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/56  

In CMakeLists.txt there was a duplicate entry for example11, which  
appears to have been there by mistake and caused following issue to  
happen:  
  
```  
CMake Error at CMakeLists.txt:72 (add_executable):  
  add_executable cannot create target "example11" because another target with  
  the same name already exists.  The existing target is an executable created  
  in source directory "/…snip…/safe_numerics/example".  
  See documentation for policy CMP0002 for more details.  
Call Stack (most recent call first):  
  example/CMakeLists.txt:9 (test_run_pass)  
  example/CMakeLists.txt:31 (test_run_pass_example)  
```  
  
It doesn't look like examples depend on the order, so I sorted them  
naturally too.

---

## Comment 1

> Username: robertramey  
> Created_at: 2018-08-02 15:31:33 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/56#issuecomment-409968440  

Thanks for catching this. I incorporated this by hand.  In the future, please use the develop branch so I can test any PRs

---

## Comment 2

> Username: janisozaur  
> Created_at: 2018-08-02 17:48:53 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/56#issuecomment-410011906  

@robertramey as the repo owner, you can select which branch a PR should target. See https://blog.github.com/2016-08-15-change-the-base-branch-of-a-pull-request/

---
