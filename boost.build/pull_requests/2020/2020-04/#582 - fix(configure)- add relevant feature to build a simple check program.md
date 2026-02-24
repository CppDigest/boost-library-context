# #582 fix(configure): add relevant feature to build a simple check program [Closed]

> Username: ledocc  
> Created at: 2020-04-27 07:26:09 UTC  
> Updated at: 2021-10-02 20:59:49 UTC  
> Closed at: 2021-02-03 16:21:48 UTC  
> Url: https://github.com/boostorg/build/pull/582  

when checking if a lib is available by linking an executable, we have to  
build with the same variant/link/define that the final build.

---

## Comment 1

> Username: ledocc  
> Created_at: 2020-05-21 20:59:11 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-632342608  

up

---

## Comment 2

> Username: ledocc  
> Created_at: 2020-06-11 07:56:55 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-642479873  

@swatanabe, can you take a look to this PR please ?

---

## Comment 3

> Username: swatanabe  
> Created_at: 2020-07-28 00:44:27 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-664710973  

AMDG  
  
This should only include propagated features.  It should not  
include <define>.  
  
I'm wondering, though, whether it would be better to drop the hard-coded  
list entirely.  No matter what set of properties we pick they'll be  
wrong for some uses.  We can generate the cache key using  
[ $(ps).base-relevant ] or similar.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: ledocc  
> Created_at: 2020-08-19 18:51:10 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-676599423  

I do this patch when I try to build boost.locale with ICU support on windows,  
My first problem was boost.locale test if ICU is available with a simple test program "has_icu".  
But boost.build use the "debug" variant even if I build boost in "release" mode. But MSVC does not allow  
to link debug with release lib/executable.   
So I add "variant" in the list of "relevant" feature.  
Same kind of issue when I build with static build of ICU that require a preprocessor DEFINE variable.   
Then I add  "define" in the list of "relevant" feature.  
Same story for link and runtime-link  
  
```  
# The configure feature allows external definition of what features are  
# relevant for doing configuration builds. One can add additional relevant  
# features by using:  
#  
#   import feature ;  
#   import configure ;  
#   feature.compose <configure> : <threading> ;  
#  
```  
I try this but no success, so I change it directly in "src/build/configure.jam" ...

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2021-02-03 16:21:48 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-772633816  

Fixed with https://github.com/boostorg/build/commit/95c875b1dd12a52f0d120480c8b498c9c3da36ab

---

## Comment 6

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:47 UTC  
> Url: https://github.com/boostorg/build/pull/582#issuecomment-932819698  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
