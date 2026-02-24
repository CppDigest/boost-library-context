# #884 - Missing change in Boost 1.80.0 Unix distributable as opposed to the Windows one [Open]

> Username: gaussianrecurrence  
> Created at: 2023-04-27 19:02:13 UTC  
> Updated at: 2024-04-11 20:12:04 UTC  
> Url: https://github.com/boostorg/boost/issues/884  

When downloading Boost 1.80.0 (Unix distributable): https://boostorg.jfrog.io/artifactory/main/release/1.80.0/source/boost_1_80_0.tar.gz  
And Boost 1.80.0 (Windows distributable): https://boostorg.jfrog.io/artifactory/main/release/1.80.0/source/boost_1_80_0.zip  
I noticed there is an small diff in **boost/dynamic_bitset/dynamic_bitset.hpp**, which matches the change introduced by boostorg/dynamic_bitset#64  
In the case of the **Windows distributable** boostorg/dynamic_bitset#64 **IS** included, but in the case of the **Unix distributable** boostorg/dynamic_bitset#64 **it is NOT** included.  
So I was wondering if there is an specific reason for this delta or if this is not supposed to be like that?

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-04-11 18:44:41 UTC  
> Url: https://github.com/boostorg/boost/issues/884#issuecomment-2050303602  

A similar problem happened today. In this case it appears to have been caused by circleci. Three jobs started at the same time.  An earlier commit took longer to finish building, and uploaded last, overwriting the newer commit.    
  
The commit-bot script may be replaced/upgraded soon.   In the new script, a feature could potentially be added to handle this case.

---

## Comment 2

> Username: glenfe  
> Created at: 2024-04-11 18:46:17 UTC  
> Url: https://github.com/boostorg/boost/issues/884#issuecomment-2050306162  

@sdarwin thank you!

---

## Comment 3

> Username: Lastique  
> Created at: 2024-04-11 20:12:03 UTC  
> Url: https://github.com/boostorg/boost/issues/884#issuecomment-2050441316  

Is it possible to run jobs of the same kind (e.g. from commits on the same branch) serially?
