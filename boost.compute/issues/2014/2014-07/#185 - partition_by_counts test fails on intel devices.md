# #185 - partition_by_counts test fails on intel devices [Closed]

> Username: kylelutz  
> Created at: 2014-07-12 15:37:55 UTC  
> Updated at: 2014-07-12 20:11:05 UTC  
> Closed at: 2014-07-12 16:49:08 UTC  
> Url: https://github.com/boostorg/compute/issues/185  

The `partition_by_counts` tests fails on Intel CPU devices:  
  
```  
../test/test_device.cpp(159): error in "partition_by_counts": check sub_devices[0].compute_units() == size_t(2) failed [1 != 2]  
../test/test_device.cpp(161): error in "partition_by_counts": check sub_devices[2].compute_units() == size_t(1) failed [2 != 1]  
```  
  
It looks like the call to `clCreateSubDevices()` with `CL_DEVICE_PARTITION_BY_COUNTS` is working fine but the order of the returned sub-devices is not guaranteed to match the order of the compute-unit counts passed into the function.  
  
We should update the test to sort and then compare the returned sub-device compute-unit counts to make sure we got the correct results regardless of order.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-07-12 16:49:08 UTC  
> Url: https://github.com/boostorg/compute/issues/185#issuecomment-48817427  

Fixed in PR #187.

---

## Comment 2

> Username: ddemidov  
> Created at: 2014-07-12 20:11:05 UTC  
> Url: https://github.com/boostorg/compute/issues/185#issuecomment-48822710  

I can confirm this fixes the issue. All tests run cleanly on all of my devices with the latest develop branch.
