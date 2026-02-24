# #411 CreateSubbuffer support [Closed]

> Username: bkchr  
> Created at: 2015-01-15 14:11:58 UTC  
> Updated at: 2015-01-16 15:15:44 UTC  
> Closed at: 2015-01-16 15:03:12 UTC  
> Url: https://github.com/boostorg/compute/pull/411  

Hi, I added the functionality to create a sub buffer out of a buffer.

---

## Comment 1

> Username: coveralls  
> Created_at: 2015-01-15 14:27:00 UTC  
> Url: https://github.com/boostorg/compute/pull/411#issuecomment-70092316  

[![Coverage Status](https://coveralls.io/builds/1748060/badge)](https://coveralls.io/builds/1748060)  
  
Coverage remained the same when pulling **1e2da6b9c82dc8fa9614d096acf1f22da2f57328 on bastiankoe:createsubbuffer** into **57eec36d20e122a496d3fbfddf8bf4ad8d6f4a4f on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2015-01-16 04:45:07 UTC  
> Url: https://github.com/boostorg/compute/pull/411#issuecomment-70207835  

Awesome! Thanks for working on this!  
  
A few small issues though:  
- Because `clCreateSubBuffer()` was added in OpenCL 1.1, this needs to be guarded with a `#ifdef CL_VERSION_1_1` check. See the [`event::set_callback()`](https://github.com/kylelutz/compute/blob/master/include/boost/compute/event.hpp#L202) method for an example of how to do this.  
- Could you add some documentation for this method? Be sure to include the `\opencl_version_warning` line so users know it is only available for OpenCL 1.1.  
- Could you add a small test-case to [`test_buffer.cpp`](https://github.com/kylelutz/compute/blob/master/test/test_buffer.cpp) to test this functionality?  
- Also, can you make a pull-request against the `develop` branch? See some documentation for this in the [Developer's Guide](https://github.com/kylelutz/compute/wiki/Developer%27s-Guide) on the wiki.  
  
After that it should be good to merge. Thanks!

---

## Comment 3

> Username: bkchr  
> Created_at: 2015-01-16 15:03:46 UTC  
> Url: https://github.com/boostorg/compute/pull/411#issuecomment-70265598  

The new pull request is opened at #412

---

## Comment 4

> Username: coveralls  
> Created_at: 2015-01-16 15:15:44 UTC  
> Url: https://github.com/boostorg/compute/pull/411#issuecomment-70267414  

[![Coverage Status](https://coveralls.io/builds/1754880/badge)](https://coveralls.io/builds/1754880)  
  
Coverage decreased (-0.08%) when pulling **7eba538c55caa278c0459972eb2c7d68d871c476 on bastiankoe:createsubbuffer** into **57eec36d20e122a496d3fbfddf8bf4ad8d6f4a4f on kylelutz:master**.

---
