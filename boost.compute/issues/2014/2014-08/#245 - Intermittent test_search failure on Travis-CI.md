# #245 - Intermittent test_search failure on Travis-CI [Closed]

> Username: kylelutz  
> Created at: 2014-08-20 07:06:34 UTC  
> Updated at: 2016-10-14 17:21:19 UTC  
> Closed at: 2016-10-13 04:41:23 UTC  
> Url: https://github.com/boostorg/compute/issues/245  

The following test failure occurs occasionally on Travis-CI:  
  
```  
test_search: /home/travis/build/kylelutz/compute/test/test_search.cpp:45: void compute_test::search_int::test_method(): Assertion `iter == vectort.begin() + 10' failed.  
Running 2 test cases...  
unknown location(0): fatal error in "search_int": signal: SIGABRT (application abort requested)  
*** 1 failure detected in test suite "TestSearch"  
```  
  
See the build here: https://travis-ci.org/kylelutz/compute/jobs/33041316  
  
We should investigate this (potentially run it on the Intel CPU OpenCL implementation under valgrind).

---

## Comment 1

> Username: ddemidov  
> Created at: 2014-08-20 07:43:03 UTC  
> Url: https://github.com/boostorg/compute/issues/245#issuecomment-52743744  

Travis-CI uses AMD implementation of OpenCL, not Intel.

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-09-12 16:53:35 UTC  
> Url: https://github.com/boostorg/compute/issues/245#issuecomment-139796571  

I got almost the same problem (but in `test_search_n.cpp`) with clang on Travis-CI (https://travis-ci.org/boostorg/compute/builds/79994943). I check it on my PC (AMD HD7770) on `master` and this error occurs both with clang and g++.  It does not happen every time.  
  
```  
Running 2 test cases...  
test_search_n: /home/jakub/git/compute/test/test_search_n.cpp:37: void compute_test::search_int::test_method(): Assertion `iter == vectort.begin() + 10' failed.  
unknown location(0): fatal error in "search_int": signal: SIGABRT (application abort requested)  
  
*** 1 failure detected in test suite "TestSearchN"  
  
```

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-09-12 17:01:58 UTC  
> Url: https://github.com/boostorg/compute/issues/245#issuecomment-139797185  

OK, I know where the bug is.  I'll push a fix in a few minutes.

---

## Comment 4

> Username: jszuppe  
> Created at: 2016-10-12 20:49:10 UTC  
> Url: https://github.com/boostorg/compute/issues/245#issuecomment-253334834  

This was fixed in https://github.com/boostorg/compute/pull/511. Can be closed.

---

## Comment 5

> Username: kylelutz  
> Created at: 2016-10-13 04:41:23 UTC  
> Url: https://github.com/boostorg/compute/issues/245#issuecomment-253413010  

Closed. Thanks!
