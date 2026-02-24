# #502 Updated to recent compilers/boost [Closed]

> Username: teeks99  
> Created at: 2025-10-29 00:02:19 UTC  
> Updated at: 2025-11-03 00:37:08 UTC  
> Closed at: 2025-11-03 00:37:08 UTC  
> Url: https://github.com/boostorg/python/pull/502  



---

## Comment 1

> Username: teeks99  
> Created_at: 2025-10-29 00:03:26 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459096836  

Fixes #501

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2025-10-29 00:18:46 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459153875  

Thanks @teeks99 , now I only need to analyze and fix the fallout of the update. Very likely some configuration mistake or tool (faber) limitation with the new compiler versions...

---

## Comment 3

> Username: stefanseefeld  
> Created_at: 2025-10-29 00:27:48 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459173616  

@teeks99,  in fact, a bunch of those failures are caused by the link step trying to use a static Python library. As the Python versions are pre-packaged into those docker images you provide, can you check whether the Python installation comes with shared (Python) libraries ?

---

## Comment 4

> Username: teeks99  
> Created_at: 2025-10-29 01:21:52 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459262686  

Where are you seeing the shared python library errors? I'm doing my own builds for python2, so I'm betting I need to add to that.  
  
It looks like all the C++98 tests are failing due to other boost modules updating.   
  
I think the python 3 failures are in the test_upcast test, [here](https://github.com/boostorg/python/actions/runs/18892704105/job/53923684049?pr=502#step:5:780)?

---

## Comment 5

> Username: e-kwsm  
> Created_at: 2025-10-29 01:25:47 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459269630  

It seems that Boost.Function requires C++11 since 1.85 (https://github.com/boostorg/function/commit/f86ddb0bb977946c64ba22b8456fb6551c6d04ac), and upcast fails since python is now 3.12 (#479).

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2025-10-29 01:34:37 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459285249  

I noticed the Python archive being used in https://github.com/boostorg/python/actions/runs/18892704105/job/53923684026?pr=502 for example. Again, it's possible that my way of linking is wrong and the archive being picked up when the DSO should be used. I need to investigate...

---

## Comment 7

> Username: teeks99  
> Created_at: 2025-10-29 01:50:05 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459306607  

I've just pushed a new image of clang, with a python 2 version that added `./configure --enable-shared`. I don't have a great way to test if it works though, running on my repo now.   
  
I'll try to get gcc's image to have the same flag in the morning. Right now something is up with ftp.gnu.org where I get the gcc source from, so holding on that.

---

## Comment 8

> Username: teeks99  
> Created_at: 2025-10-29 02:00:22 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3459320598  

:sigh: that didn't seem to work, I'll have to poke around more at another time

---

## Comment 9

> Username: teeks99  
> Created_at: 2025-10-29 11:13:39 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3460981058  

I've made another change to the clang image, running a `ldconfig` after installing the shared objects seems to work, I was able to get clang with python2 past the build stage.   
  
I'm still seeing infrastructure problems in the gcc repos, so that one isn't available yet. I'll update when it has the same fix.

---

## Comment 10

> Username: teeks99  
> Created_at: 2025-10-31 01:21:54 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3470930902  

I finally got a decent gcc-15 build, that has been pushed as well, it looks like everything (except the C++98 jobs) is getting past build and into test.

---

## Comment 11

> Username: stefanseefeld  
> Created_at: 2025-11-03 00:37:08 UTC  
> Url: https://github.com/boostorg/python/pull/502#issuecomment-3478579889  

I've integrated this into https://github.com/boostorg/python/pull/505, where I've also addressed all the fallout of the upgrade.

---
