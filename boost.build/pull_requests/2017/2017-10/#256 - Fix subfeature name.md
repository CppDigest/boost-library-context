# #256 Fix subfeature name [Closed]

> Username: jzmaddock  
> Created at: 2017-10-26 10:09:57 UTC  
> Updated at: 2021-10-02 22:08:29 UTC  
> Closed at: 2017-10-29 03:40:49 UTC  
> Url: https://github.com/boostorg/build/pull/256  

When using `cxxstd-dialect=` gcc.jam simply appends the version number to the dialect name, but -std=gnu03 isn't a valid option for C++ builds (only Objective C), we want to generate `-std=gnu++03` etc.  Hence the change of subfeature name.  As far as I can tell no one is actually using this (and if they are it isn't doing what they want), so the change should be safe.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2017-10-29 03:40:49 UTC  
> Url: https://github.com/boostorg/build/pull/256#issuecomment-340235340  

Fixed in a different way. Thanks for noticing.

---
