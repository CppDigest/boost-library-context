# #574 Provide wait_list::reserve() method [Closed]

> Username: ddemidov  
> Created at: 2016-03-18 08:03:49 UTC  
> Updated at: 2016-03-18 19:33:49 UTC  
> Closed at: 2016-03-18 19:28:35 UTC  
> Url: https://github.com/boostorg/compute/pull/574  

This should help to get rid of unnecessary heap allocations.  
  
The wait lists are usually small, which means that just pushing back  
elements without prior call to reserve has very high chance of hitting  
capacity limit every time [wait_list::insert](https://github.com/boostorg/compute/blob/38ba1c54479869c6ad6d2ea1f828e6d113bc5ab9/include/boost/compute/utility/wait_list.hpp#L124) is called.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-18 13:12:10 UTC  
> Url: https://github.com/boostorg/compute/pull/574#issuecomment-198348792  

POCL builds on Travis-CI failed because of a bug in POCL (introduced by https://github.com/pocl/pocl/commit/e796cd101378b2eb9a76442e6fa56edfb4b49b24). I'm pretty sure it was fixed by https://github.com/pocl/pocl/commit/c3e93fbcad4cf1174600c959a4db6972f0533567. Disadvantage of using master branch. There's no need to retry Travis-CI builds.

---
