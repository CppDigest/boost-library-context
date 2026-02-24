# #96 - test_unique fails sporadically on travis-ci [Closed]

> Username: kylelutz  
> Created at: 2014-04-19 18:13:02 UTC  
> Updated at: 2014-04-20 23:45:11 UTC  
> Closed at: 2014-04-20 23:45:11 UTC  
> Url: https://github.com/boostorg/compute/issues/96  

The `test_unique` unit test fails sporadically on Travis-CI (https://travis-ci.org/kylelutz/compute/jobs/23285254) with a message like the following:  
  
```  
60/95 Test #60: algorithm.unique .......................***Failed    0.97 sec  
Running 1 test case...  
/home/travis/build/kylelutz/compute/test/test_unique.cpp(33): error in "unique_int": check { actual, actual + 7 } == { expected, expected + 7 } failed.   
Mismatch in a position 3: 4 != 2  
*** 1 failure detected in test suite "TestUnique"  
```  
  
This only seems to happen on CPU implementations.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-19 18:14:06 UTC  
> Url: https://github.com/boostorg/compute/issues/96#issuecomment-40876537  

@roshanr95 Can you look into this and try to reproduce it?

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-04-20 02:27:17 UTC  
> Url: https://github.com/boostorg/compute/issues/96#issuecomment-40886075  

Will look into this. Can I force it to run on CPU somehow?

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-04-20 02:53:12 UTC  
> Updated at: 2014-04-20 02:53:48 UTC  
> Url: https://github.com/boostorg/compute/issues/96#issuecomment-40886429  

If you're running on an OpenCL implementation with CPU support (e.g. AMD or Intel) then you can just set the `BOOST_COMPUTE_DEFAULT_DEVICE` environment variable to the name of the CPU device (run `./example/list_devices` to see all the names). Run the `./example/hello_world` example to ensure the correct CPU device is used as the default device.  
  
See https://github.com/kylelutz/compute/wiki/Developer%27s-Guide#environment-variables for more information.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-04-20 23:45:11 UTC  
> Url: https://github.com/boostorg/compute/issues/96#issuecomment-40908070  

Fixed in PR #97. Thanks Roshan!
