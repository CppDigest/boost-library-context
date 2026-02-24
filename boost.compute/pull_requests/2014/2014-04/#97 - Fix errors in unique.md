# #97 Fix errors in unique [Merged]

> Username: roshanrags  
> Created at: 2014-04-20 04:14:49 UTC  
> Updated at: 2014-04-21 02:48:25 UTC  
> Merged at: 2014-04-20 23:42:56 UTC  
> Closed at: 2014-04-20 23:42:56 UTC  
> Url: https://github.com/boostorg/compute/pull/97  

Fixes #96 on my setup...

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-04-20 04:31:57 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40887656  

[![Coverage Status](https://coveralls.io/builds/696046/badge)](https://coveralls.io/builds/696046)  
  
Coverage remained the same when pulling **c99d2bf58d702afe0f33355788df67832037f1a6 on roshanr95:unique** into **8b06e3f7bbf94c5fd5c6109cba4e429bd27a9f03 on kylelutz:develop**.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-04-20 11:29:47 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40892977  

I don't believe this changes anything, since `boost::is_same<uint, boost::compute::uint_>::value` is true. And indeed, running `test_unique` with the patch applied on my system gives:  
  
```  
$ export BOOST_COMPUTE_DEFAULT_DEVICE=Intel  
$ while ./test_unique ; do echo; done  
Running 1 test case...  
  
*** No errors detected  
  
Running 1 test case...  
  
*** No errors detected  
  
Running 1 test case...  
/home/demidov/work/opencl/compute/test/test_unique.cpp(33): error in "unique_int": check { actual, actual + 7 } == { expected, expected + 7 } failed.   
Mismatch in a position 3: 4 != 2  
  
*** 1 failure detected in test suite "TestUnique"  
```

---

## Comment 3

> Username: ddemidov  
> Created_at: 2014-04-20 11:50:30 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40893326  

When I dump the source of the kernels being built and diff the results between CPU and GPU, the only difference is the scan kernel that is used. For the CPU it is `scan_on_cpu`. Could this be the culprit?

---

## Comment 4

> Username: roshanrags  
> Created_at: 2014-04-20 16:15:11 UTC  
> Updated_at: 2014-04-20 16:22:44 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40898280  

On the host side, `cl_uint` is guaranteed to be the same type(read size) as `uint` on the device side. The normal `uint` on the host side is, however, platform dependent. (See #84)  
In any case, I used to get `Mismatch in a position 2: 2 != 4` and I'm not able to reproduce it now. Maybe something else also needs to be fixed?

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-04-20 16:37:02 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40898848  

Ahh, I see the issue. In `unique()` we do:  
  
```  
gather(temp.begin(), iter, first, first, queue);  
```  
  
But `gather()` doesn't support overlapped input and output ranges thus we see incorrect data being copied every once in a while. The code you have now is good and should be added but I think we also need to make a temporary buffer/vector for the input data and call `gather()` with the copied values as the input range.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-04-20 16:59:35 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40899387  

And, also, this may be a good time to implement `unique_copy()` which would be almost exactly like `unique()` is now. Then `unique()` would just make a temporary copy of the input range and then call `unique_copy()`.

---

## Comment 7

> Username: coveralls  
> Created_at: 2014-04-20 20:46:50 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40904477  

[![Coverage Status](https://coveralls.io/builds/696911/badge)](https://coveralls.io/builds/696911)  
  
Coverage remained the same when pulling **59efaa0eeb60d6b6a5d6e7d82d97cca2f1d783ca on roshanr95:unique** into **a78212fdde9254c18cabacd4388ca7106dbbcdbd on kylelutz:develop**.

---

## Comment 8

> Username: roshanrags  
> Created_at: 2014-04-20 21:01:07 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40904766  

Should be fixed now...

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-04-20 23:43:06 UTC  
> Url: https://github.com/boostorg/compute/pull/97#issuecomment-40908033  

Awesome! Thanks!

---
