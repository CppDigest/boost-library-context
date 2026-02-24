# #187 Fix partition_by_counts test for Intel CPUs [Merged]

> Username: kylelutz  
> Created at: 2014-07-12 16:01:27 UTC  
> Updated at: 2014-07-12 16:48:54 UTC  
> Merged at: 2014-07-12 16:48:52 UTC  
> Closed at: 2014-07-12 16:48:52 UTC  
> Url: https://github.com/boostorg/compute/pull/187  

This fixes a test failure in partition_by_counts for Intel CPU  
devices. The clCreateSubDevices() function is not guaranteed to  
return the sub-devices in the same order as they were specified  
in the compute unit counts argument.  
  
Now the test sorts the returned sub-devices by number of compute  
units and checks that each specified count is present.  
  
See issue #185.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-12 16:34:44 UTC  
> Url: https://github.com/boostorg/compute/pull/187#issuecomment-48817022  

[![Coverage Status](https://coveralls.io/builds/960274/badge)](https://coveralls.io/builds/960274)  
  
Coverage remained the same when pulling **86199873b729ece3fdb7b5ca7816e55d8d08bbde on fix-partition-by-counts-test** into **5cfda5412efefe1069c9f5dd1485c4cb8e92860d on develop**.

---
