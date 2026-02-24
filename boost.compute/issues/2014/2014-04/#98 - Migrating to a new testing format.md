# #98 - Migrating to a new testing format [Closed]

> Username: roshanrags  
> Created at: 2014-04-20 21:08:40 UTC  
> Updated at: 2014-04-28 18:04:51 UTC  
> Closed at: 2014-04-28 18:04:51 UTC  
> Url: https://github.com/boostorg/compute/issues/98  

Should we migrate to a new testing format that will run the tests on all available devices, something like [this](https://gist.github.com/roshanr95/30cd60edf6fccdb4e8cd)? Basically, it uses code from `example/list_devices` to iterate through all compute devices available and runs the test on each of them.  
  
Pros:  
Better coverage  
Something like #96 wouldn't happen again  
  
Cons:  
Travis takes more time  
May be redundant in a lot of tests

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-21 15:37:21 UTC  
> Url: https://github.com/boostorg/compute/issues/98#issuecomment-40944727  

I think it's a good idea to automatically run the tests on all available devices but I think we should find a way to make it more automatic than manually iterating though each device in each test case.  
  
Also, there is some global setup performed in `test/context_setup.hpp` which would have to be re-done to allow the device to change dynamically. We should look into what Boost.Test offers for dynamically running tests with different parameters.  
  
Alternatively, we could find a way to make CMake/CTest detect which the devices are present and then call each test multiple times with each device by setting the `BOOST_COMPUTE_DEFAULT_DEVICE` environment variable.

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-04-28 04:31:57 UTC  
> Url: https://github.com/boostorg/compute/issues/98#issuecomment-41522252  

I've been going through the documentation of Boost.Test. The closest thing I could find which we can make use of is `BOOST_PARAM_TEST_CASE`. The only problem I see with using this is writing tests is no longer as trivial as simply putting a `BOOST_AUTO_TEST_CASE` on top. It would require manual registration of each test with the master suite.  
  
Even if we were to wrap everything up into macros, it would require multiple macros and tests to be something like  
  
```  
test_function1(device)  
{  
    // Get queue from device  
  
    // Do testing on queue  
}  
  
test_function2(device)  
{  
    // Do testing on queue  
}  
  
bool init_unit_test()  
{  
    // make vector of devices  
  
    // add the test functions to the master suite one by one  
    // using BOOST_PARAM_TEST_CASE  
  
    return true;  
}  
```  
  
Wrapping it up in macros would result in something like  
  
```  
START_TEST(name1)  
// Do testing on queue  
END_TEST  
  
START_TEST(name2)  
// Do testing on queue  
END_TEST  
  
START_REGISTRATION  
REGISTER(name1)  
REGISTER(name2)  
END_REGISTRATION  
```  
  
I feel this is not that big a deviation from the way tests are being written now and can be made easy to use with the help of good macro names/documentation/examples. Your thoughts on this?

---

## Comment 3

> Username: ddemidov  
> Created at: 2014-04-28 04:37:31 UTC  
> Url: https://github.com/boostorg/compute/issues/98#issuecomment-41522419  

My 2cents: I think this kind of thing is useless for the automated testing on Travis-CI, because they only have one compute device. Locally I do test all possible combinations of compilers/OpenCL SDK with a pair of simple bash scripts. [build_init](https://github.com/ddemidov/vexcl/blob/master/build_init) creates and initializes build directories for a set of compilers, and [build_n_test](https://github.com/ddemidov/vexcl/blob/master/build_n_test) builds and runs the tests for a given set of OpenCL platforms. The selection of platform is done through environment variable.

---

## Comment 4

> Username: roshanrags  
> Created at: 2014-04-28 05:08:55 UTC  
> Url: https://github.com/boostorg/compute/issues/98#issuecomment-41523428  

True, Travis seems to mostly have AMD CPUs only. Also, gpu-exclusive functions like `scan_on_gpu` have zero coverage. I guess we can't do anything about this unless Travis adds GPUs also.  
  
@ddemidov nice scripts btw..

---

## Comment 5

> Username: roshanrags  
> Created at: 2014-04-28 18:04:51 UTC  
> Url: https://github.com/boostorg/compute/issues/98#issuecomment-41592176  

Confirmed with Travis support that it cannot be done at the moment. However, it may be possible in the future with Travis planning to add support for KVMs.
