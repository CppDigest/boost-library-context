# #77 set sharedlib version on aix when aix-soname=svr4 [Closed]

> Username: haubi  
> Created at: 2015-04-23 11:41:01 UTC  
> Updated at: 2017-10-30 15:36:45 UTC  
> Closed at: 2017-10-30 15:36:44 UTC  
> Url: https://github.com/boostorg/boost/pull/77  

This patch is to actually set the sharedlib version for filename-based shared library versioning on AIX from https://github.com/boostorg/build/pull/70

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-26 14:23:05 UTC  
> Url: https://github.com/boostorg/boost/pull/77#issuecomment-339682656  

@danieljames should this PR be closed?  It is targeting the wrong branch and the wrong project.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-10-30 15:23:08 UTC  
> Url: https://github.com/boostorg/boost/pull/77#issuecomment-340478817  

@grafikrobot What do you think about this one?

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2017-10-30 15:31:47 UTC  
> Url: https://github.com/boostorg/boost/pull/77#issuecomment-340481647  

@pdimov should be closed. (1) it's against master, (2) I don't know what it does, (3) it also changes the submodule when it shouldn't.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-10-30 15:34:20 UTC  
> Url: https://github.com/boostorg/boost/pull/77#issuecomment-340482509  

Sure, but it's referencing an accompanying AIX-related change in Boost.Build, so I was wondering.

---

## Comment 5

> Username: pdimov  
> Created_at: 2017-10-30 15:36:44 UTC  
> Url: https://github.com/boostorg/boost/pull/77#issuecomment-340483305  

On the other hand, the Build PR was also closed.

---
