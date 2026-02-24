# #153 Minor CMake path changes [Closed]

> Username: nemothenoone  
> Created at: 2019-04-08 23:30:59 UTC  
> Updated at: 2019-11-07 05:09:23 UTC  
> Closed at: 2019-11-07 05:09:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/153  

Pull request for the issue #152.  
  
Minor CMake enhancements and fixes are about strict standard-compliant option type (```INTEGER``` -> ```STRING```) and path fixes for CMake project being built inside of boost superproject source tree.  
  
Try it yourself. Clone ```https://github.com/boostorg/boost``` and ```cmake -G Xcode ${BOOST_ROOT}/libs/serialization/CMake``` from some directory. Without this minor fix, it leads me to the error like ```CMake Error at CMakeLists.txt:106 (message): BOOST_ROOT not found```

---

## Comment 1

> Username: robertramey  
> Created_at: 2019-09-29 15:22:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/153#issuecomment-536312350  

making the change to line 103, adding another level of ..,  results in my getting BOOST_ROOT not found error.  We're out of sync somehow.

---
