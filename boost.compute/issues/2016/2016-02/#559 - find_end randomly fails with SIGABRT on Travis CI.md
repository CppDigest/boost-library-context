# #559 - find_end randomly fails with SIGABRT on Travis CI [Closed]

> Username: jszuppe  
> Created at: 2016-02-28 18:53:37 UTC  
> Updated at: 2016-04-26 18:32:32 UTC  
> Closed at: 2016-04-26 18:32:30 UTC  
> Url: https://github.com/boostorg/compute/issues/559  

I don't know why, but `find_end` algorithm sometimes fails with with SIGABRT in `find_end_string` test case. It happens rarely and only with AMD APP SDK (both 2.9.1 and 3.0) on Travis CI (I have an Intel CPU like Travis CI and I wasn't able to reproduce on my PC.) and only on the second `ctest --output-on-failure` run. I might be AMD APP SDK issue. I'll investigate it.   
  
```  
32/165 Test  #32: algorithm.find_end ...................***Failed    0.17 sec  
test_find_end: /home/travis/build/haahh/compute/test/test_find_end.cpp:60: void compute_test::find_end_string::test_method(): Assertion `iter == vectort.begin() + 15' failed.  
Running 2 test cases...  
unknown location(0): fatal error in "find_end_string": signal: SIGABRT (application abort requested)  
```  
  
Examples:   
- https://travis-ci.org/boostorg/compute/jobs/112436799 (develop)  
- https://travis-ci.org/haahh/compute/jobs/112264011#L7337

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-04-26 18:32:30 UTC  
> Url: https://github.com/boostorg/compute/issues/559#issuecomment-214841859  

Fixed by https://github.com/boostorg/compute/pull/597.
