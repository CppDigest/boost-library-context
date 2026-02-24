# #29 Fix test, by closing the archive-output-stream before reading archive [Closed]

> Username: DenizThatMenace  
> Created at: 2020-10-22 13:36:52 UTC  
> Updated at: 2020-11-04 11:27:22 UTC  
> Closed at: 2020-11-04 11:27:22 UTC  
> Url: https://github.com/boostorg/msm/pull/29  

This fixes the test `my_test` which fails on OSX with the following error-message:  
```  
Running 1 test case...  
  
unknown location:0: fatal error: in "my_test": boost::archive::archive_exception: array size too short  
  
libs/msm/test/SerializeWithHistory.cpp:327: last checkpoint  
  
*** 1 failure is detected in the test module "MyTest"  
```

---

## Comment 1

> Username: DenizThatMenace  
> Created_at: 2020-11-04 11:27:22 UTC  
> Url: https://github.com/boostorg/msm/pull/29#issuecomment-721677097  

Closing as now obsolete.

---
