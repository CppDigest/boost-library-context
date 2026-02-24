# #726 Declare the /python//numpy target to hold the include path (fixes #725) [Closed]

> Username: pdimov  
> Created at: 2021-05-24 19:13:22 UTC  
> Updated at: 2025-12-20 22:42:58 UTC  
> Closed at: 2021-05-25 03:29:27 UTC  
> Url: https://github.com/boostorg/build/pull/726  

This declares a `/python//numpy` target in `python.jam` to hold the NumPy include path; the current practice of using a variable does not support more than one Python installation. See issue #725.

---

## Comment 1

> Username: SoapGentoo  
> Created_at: 2021-05-24 20:58:59 UTC  
> Url: https://github.com/boostorg/build/pull/726#issuecomment-847329937  

I've verified that this patch works with multiple python implementations, please merge it.

---

## Comment 2

> Username: thesamesam  
> Created_at: 2021-05-24 21:43:18 UTC  
> Url: https://github.com/boostorg/build/pull/726#issuecomment-847355490  

I had the same issue but this patch (combined with the Boost Python fix) allows a successful build against the correct numpy headers:  
  
  
```  
"x86_64-pc-linux-gnu-g++"   -fvisibility-inlines-hidden -O2 -pipe -march=native -fdiagnostics-color=always -frecord-gcc-switches -std=c++14 -fPIC -m64 -pthread -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_NUMPY_SOURCE -DNDEBUG  -I"." -I"/usr/include/python3.9" -I"/usr/lib/python3.9/site-packages/numpy/core/include"  -c -o "bin.v2/libs/python/build/gcc-11.1/gentoorelease/pch-off/python-3.9/threading-multi/visibility-hidden/numpy/ufunc.o" "libs/python/src/numpy/ufunc.cpp"  
[...]  
"x86_64-pc-linux-gnu-g++"   -fvisibility-inlines-hidden -O2 -pipe -march=native -fdiagnostics-color=always -frecord-gcc-switches -std=c++14 -fPIC -m64 -pthread -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_NUMPY_SOURCE -DNDEBUG  -I"." -I"/usr/include/python3.8" -I"/usr/lib/python3.8/site-packages/numpy/core/include"  -c -o "bin.v2/libs/python/build/gcc-11.1/gentoorelease/pch-off/python-3.8/threading-multi/visibility-hidden/numpy/ufunc.o" "libs/python/src/numpy/ufunc.cpp"  
```  
  
Downstream bug in Gentoo: https://bugs.gentoo.org/733830.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2021-05-25 02:53:55 UTC  
> Url: https://github.com/boostorg/build/pull/726#issuecomment-847490717  

Applied as https://github.com/bfgroup/b2/commit/8b923d8eaf9f26f436485cc4070c53095a578b48

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:36 UTC  
> Url: https://github.com/boostorg/build/pull/726#issuecomment-932819673  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
