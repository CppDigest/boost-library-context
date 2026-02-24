# #760 - drone caching failures [Closed]

> Username: alandefreitas  
> Created at: 2023-06-22 12:52:17 UTC  
> Updated at: 2023-07-20 13:58:59 UTC  
> Closed at: 2023-07-20 13:58:59 UTC  
> Url: https://github.com/boostorg/url/issues/760  

Caching in drone job is failing when b2 updates  
  
```  
error: mismatched versions of B2 engine and core  
error: B2 engine (./b2) is 4.9.6  
error: B2 core (at /drone/boost-root/tools/build/src) is 4.10-git  
```

---

## Comment 1

> Username: sdarwin  
> Created at: 2023-06-24 21:03:31 UTC  
> Url: https://github.com/boostorg/url/issues/760#issuecomment-1605730956  

https://github.com/boostorg/boost-ci/blob/master/ci/common_install.sh is purposely skipping the rebuild of b2 to save time in a caching scenario.  What could be done is add logic in that section of common_install.sh to compare b2 versions and rebuild if they are mismatched.

---

## Comment 2

> Username: sdarwin  
> Created at: 2023-06-26 14:22:48 UTC  
> Url: https://github.com/boostorg/url/issues/760#issuecomment-1607600580  

Pull request 213 in boostorg/boost-ci

---

## Comment 3

> Username: sdarwin  
> Created at: 2023-06-28 22:41:21 UTC  
> Url: https://github.com/boostorg/url/issues/760#issuecomment-1612207268  

The boost-ci PR was merged.  This issue should be fixed now.
