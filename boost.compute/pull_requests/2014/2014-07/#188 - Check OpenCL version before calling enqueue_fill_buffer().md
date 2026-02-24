# #188 Check OpenCL version before calling enqueue_fill_buffer() [Closed]

> Username: kylelutz  
> Created at: 2014-07-12 18:09:54 UTC  
> Updated at: 2014-07-12 19:26:04 UTC  
> Closed at: 2014-07-12 18:53:46 UTC  
> Url: https://github.com/boostorg/compute/pull/188  

This adds a check for OpenCL version 1.2 before calling the  
enqueue_fill_buffer() function in the fill() algorithm.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-07-12 18:10:51 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48819591  

@ddemidov this should fix some of the test failures you were seeing on the K40c.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-07-12 18:17:49 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48819772  

I would move `check_device_version()` to `compute::device`. I am preparing a PR that would skip some tests based on device version. Failing tests in `test_device.cpp` do not have a queue to call the check on.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-07-12 18:23:05 UTC  
> Updated_at: 2014-07-12 18:42:33 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48819894  

Done. `device` now has a `check_version()` method (which `command_queue` now uses for its `check_device_version()` method).

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-07-12 18:41:31 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48820395  

[![Coverage Status](https://coveralls.io/builds/960461/badge)](https://coveralls.io/builds/960461)  
  
Coverage remained the same when pulling **dfe9399c9f5484525d4398cf72e89c1fb65435fc on check-fill-buffer** into **2540895ebf6cb488b01604097afdf68f35aaf5d4 on develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-07-12 18:53:46 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48820746  

Closed in favor of PR #189.

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-07-12 19:00:27 UTC  
> Url: https://github.com/boostorg/compute/pull/188#issuecomment-48820918  

[![Coverage Status](https://coveralls.io/builds/960517/badge)](https://coveralls.io/builds/960517)  
  
Coverage remained the same when pulling **dfe9399c9f5484525d4398cf72e89c1fb65435fc on check-fill-buffer** into **2540895ebf6cb488b01604097afdf68f35aaf5d4 on develop**.

---
