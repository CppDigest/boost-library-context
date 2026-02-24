# #1118 - Tests are failing on circleCI [Closed]

> Username: vissarion  
> Created at: 2023-03-10 14:33:47 UTC  
> Updated at: 2023-03-31 07:51:25 UTC  
> Closed at: 2023-03-31 07:51:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1118  

There are some buffer tests failing on [circleCI](https://app.circleci.com/pipelines/github/boostorg/geometry/376/workflows/7fc3c42a-2027-4634-a8a4-ee1569474c83/jobs/19235). It seems that they fail after this [commit](https://github.com/boostorg/geometry/commit/6a7224e369b958765daa719435371978863efe15).   
  
The same test ([algorithms_buffer_geo_spheroid](https://github.com/boostorg/geometry/blob/develop/test/algorithms/buffer/buffer_geo_spheroid.cpp)) is failing with the same error in several machines in [Boost regression matrix](https://www.boost.org/development/tests/develop/developer/geometry.html) for example [in this](https://www.boost.org/development/tests/develop/developer/output/teeks99-dkr-dg6-boost-bin-v2-libs-geometry-test-algorithms-buffer-algorithms_buffer_geo_spheroid-test-gcc-6~c++14-debug-debug-symbols-off-threading-multi-visibility-hidden.html).  
  
For now I cannot reproduce it in my system (Ubuntu 22). Tried with clang-10,11,12,13 or gcc-6,7,8,9,10.

---

## Comment 1

> Username: vissarion  
> Created at: 2023-03-10 14:34:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1118#issuecomment-1463890896  

There is a similar issue with runtime errors in https://github.com/boostorg/geometry/issues/1115
