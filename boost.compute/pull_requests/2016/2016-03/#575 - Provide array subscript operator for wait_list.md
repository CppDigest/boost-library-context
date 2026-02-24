# #575 Provide array subscript operator for wait_list [Closed]

> Username: ddemidov  
> Created at: 2016-03-18 08:49:40 UTC  
> Updated at: 2016-03-18 19:34:07 UTC  
> Closed at: 2016-03-18 19:28:26 UTC  
> Url: https://github.com/boostorg/compute/pull/575  



---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-18 12:12:29 UTC  
> Url: https://github.com/boostorg/compute/pull/575#issuecomment-198326534  

POCL builds on Travis-CI failed because of a bug in POCL (introduced by https://github.com/pocl/pocl/commit/e796cd101378b2eb9a76442e6fa56edfb4b49b24). I'm pretty sure it was fixed by https://github.com/pocl/pocl/commit/c3e93fbcad4cf1174600c959a4db6972f0533567. Disadvantage of using master branch. There's no need to retry Travis-CI builds.

---
