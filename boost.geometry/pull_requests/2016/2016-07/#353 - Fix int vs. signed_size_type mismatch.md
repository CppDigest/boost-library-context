# #353 Fix int vs. signed_size_type mismatch [Merged]

> Username: jhunold  
> Created at: 2016-07-25 14:28:21 UTC  
> Updated at: 2016-08-03 06:18:13 UTC  
> Merged at: 2016-08-03 06:18:13 UTC  
> Closed at: 2016-08-03 06:18:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/353  

Introduced in f5b4e4a9fa0e491fea4644227f9e7499e0f8f36e  
For a failing regression test take a look at [clang-linux-3.8~c++1z](http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-8-1z-Docker-64on64-boost-bin-v2-libs-geometry-test-algorithms-buffer-algorithms_buffer-test-clang-linux-3-8~c++1z-debug.html) for example.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2016-08-03 00:22:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/353#issuecomment-237092035  

Thanks!  
Barend are you ok with this fix?  
  
In `traversal_switch_detector` there is also regions map keeping region ids of type `int`. Should these IDs also be changed to `signed_size_type`?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2016-08-03 06:17:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/353#issuecomment-237152062  

Sure - did notice it too, but it was also fixed in another Pr so did not fix it myself  
  
Thanks!

---
