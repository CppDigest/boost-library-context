# #28 Make find_package() call optional to allow for boost submodule installations [Closed]

> Username: 0xFEEDC0DE64  
> Created at: 2025-02-12 15:31:58 UTC  
> Updated at: 2025-03-25 12:27:20 UTC  
> Closed at: 2025-03-25 12:27:20 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/28  

Thanks for the great library,  
we need boost to be a submodule of our repository so that we don't use different boost versions provided by the different distributions. We pinned the boost version by converting it to a submodule.  
  
Problem with async-mqtt5 is it tries to find it system-wide and we dont even install boost anywhere, so find_package() doesnt work.  
  
async-mqtt5 also is just a submodule in our repository.  
  
My change makes it possible to manually disable the find_package call so it doesnt fail or doesnt use incorrect boost versions.

---

## Comment 1

> Username: biljazovic  
> Created_at: 2025-02-12 15:50:50 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/28#issuecomment-2654125058  

Hi, thanks for the PR.  
  
Changes from the most recent commit 7d18f20e56f7acb8269c9fc989765c08972e7b77 should already work for you.

---

## Comment 2

> Username: 0xFEEDC0DE64  
> Created_at: 2025-02-18 14:52:33 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/28#issuecomment-2665945884  

How does your change detect if it should use find_package() or not? Do I still need to define some variable or does it work automatically?

---

## Comment 3

> Username: biljazovic  
> Created_at: 2025-02-18 15:25:23 UTC  
> Url: https://github.com/boostorg/mqtt5/pull/28#issuecomment-2666043647  

It uses `find_package` only when boost_mqtt5 is the root CMake project. [Here](https://github.com/boostorg/mqtt5/blob/43845343a280e7d72bae02a304e907e58f6c76d0/test/cmake_subdir_test/CMakeLists.txt) is an example of including boost_mqtt5 in a project when `find_package(Boost)` is not triggered.

---
