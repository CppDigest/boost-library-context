# #943 Revert "CircleCI: update image" [Merged]

> Username: pdimov  
> Created at: 2024-08-21 17:39:01 UTC  
> Updated at: 2024-08-21 17:39:34 UTC  
> Merged at: 2024-08-21 17:39:22 UTC  
> Closed at: 2024-08-21 17:39:22 UTC  
> Url: https://github.com/boostorg/boost/pull/943  

Reverts boostorg/boost#941  
  
Because of  
  
```  
# /root> '7z' 'a' '-bd' '-mx=7' '-ms=on' '-x!boost_1_87_0/ci_boost_common.py' '-x!boost_1_87_0/ci_boost_release.py' 'boost_1_87_0-snapshot.7z' 'boost_1_87_0'  
*** buffer overflow detected ***: terminated  
```

---
