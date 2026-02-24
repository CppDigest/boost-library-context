# #75 Unique algorithm [Merged]

> Username: roshanrags  
> Created at: 2014-03-24 01:20:33 UTC  
> Updated at: 2014-04-03 06:40:14 UTC  
> Merged at: 2014-03-24 04:24:05 UTC  
> Closed at: 2014-03-24 04:24:05 UTC  
> Url: https://github.com/boostorg/compute/pull/75  

Added unique() algorithm, tests and benchmarks. Removed unused variable  
in scan_on_gpu() to remove warnings

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-03-24 01:32:50 UTC  
> Url: https://github.com/boostorg/compute/pull/75#issuecomment-38406256  

[![Coverage Status](https://coveralls.io/builds/618085/badge)](https://coveralls.io/builds/618085)  
  
Coverage remained the same when pulling **1e81b7ec2e34fce61ecbb68d50edc91fdbca6fc3 on roshanr95:unique** into **667aa9c200bc845855f20f5e23f5887359966d5f on kylelutz:develop**.

---

## Comment 2

> Username: roshanrags  
> Created_at: 2014-03-24 01:36:21 UTC  
> Url: https://github.com/boostorg/compute/pull/75#issuecomment-38406373  

Looks like travis had a connection problem. Can you rerun the clang job?

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-03-24 04:24:16 UTC  
> Url: https://github.com/boostorg/compute/pull/75#issuecomment-38411458  

Looks good! Merged!

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-04-01 06:31:53 UTC  
> Url: https://github.com/boostorg/compute/pull/75#discussion_r11148830  

@roshanr95 I was looking at this and I think the `device_vector` should be reset to it's original values (or new random values) each iteration of the loop. Now it will locate and remove the duplicate items once and every other loop iteration will do less work than the first. Can you update this?

---

## Comment 5

> Username: roshanrags  
> Created_at: 2014-04-01 14:16:34 UTC  
> Url: https://github.com/boostorg/compute/pull/75#issuecomment-39210101  

Thanks, totally overlooked this. Will fix it.

---
