# #453 Performance tests for AMD's Bolt library [Merged]

> Username: jszuppe  
> Created at: 2015-05-01 11:02:25 UTC  
> Updated at: 2015-05-07 08:00:09 UTC  
> Merged at: 2015-05-07 04:54:41 UTC  
> Closed at: 2015-05-07 04:54:41 UTC  
> Url: https://github.com/boostorg/compute/pull/453  

This addresses issue https://github.com/kylelutz/compute/issues/220.  
  
I've added:  
- BOOST_COMPUTE_HAVE_BOLT option  
- FindBolt.cmake cmake module from https://github.com/HSA-Libraries/Bolt  
- Bolt perf tests (10):  
  - bolt_accumulate (reduce)  
  - bolt_count  
  - bolt_exclusive_scan  
  - bolt_fill  
  - bolt_inner_product  
  - bolt_max_element  
  - bolt_merge  
  - bolt_partial_sum (inclusive scan)  
  - bolt_saxpy (transform)  
  - bolt_sort  
- Bolt perf tests in python benchmarking script perf.py  
- Missing copyright and license info in one perf test  
  
Bolt requires C++11, so performance tests for Bolt are build only if BOOST_COMPUTE_USE_CPP11 option is enabled. if BOOST_COMPUTE_HAVE_BOLT option is on and BOOST_COMPUTE_USE_CPP11 is off, warning message is printed.  
  
If you're trying to test this PR and have problems with building Bolt on 64-bit Linux, contact me. I had the same problem.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2015-05-01 11:24:42 UTC  
> Url: https://github.com/boostorg/compute/pull/453#issuecomment-98105840  

Should I add Bolt benchmarks to perfdoc.py?

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-05-02 16:42:53 UTC  
> Url: https://github.com/boostorg/compute/pull/453#issuecomment-98377270  

Yeah, if you could add it to `perfdoc.py` that would be great. Also, could you rebase this on the latest `develop` branch, it seems there may be a merge conflict. Thanks!

---

## Comment 3

> Username: jszuppe  
> Created_at: 2015-05-02 17:51:30 UTC  
> Url: https://github.com/boostorg/compute/pull/453#issuecomment-98382535  

OK, it's done.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2015-05-07 04:54:54 UTC  
> Url: https://github.com/boostorg/compute/pull/453#issuecomment-99717844  

Looks great! Merged!

---
