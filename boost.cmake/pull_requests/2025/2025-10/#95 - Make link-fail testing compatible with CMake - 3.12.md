# #95 Make link-fail testing compatible with CMake < 3.12 [Closed]

> Username: Flamefire  
> Created at: 2025-10-09 17:36:11 UTC  
> Updated at: 2025-10-10 08:52:22 UTC  
> Closed at: 2025-10-10 07:55:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/95  

OBJECT libraries can only link to targets since 3.12  
> Object library target "foo" may not link to anything.  
  
See https://github.com/boostorg/cmake/actions/runs/18368228482/job/52325234138  
  
Use a complete build and link instead

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-10 07:55:59 UTC  
> Url: https://github.com/boostorg/cmake/pull/95#issuecomment-3388723770  

Thanks.  
  
I added a CI job to expose the issue, and then fixed it in a slightly different way (used a STATIC library instead of OBJECT to ensure the sources can be compiled.)

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-10 08:51:58 UTC  
> Updated_at: 2025-10-10 08:52:22 UTC  
> Url: https://github.com/boostorg/cmake/pull/95#issuecomment-3388937954  

> used a STATIC library instead of OBJECT  
  
I considered that but wasn't fully sure if that could cause issues on some compiler with symbol visibility when linking to the exe or if it could fail before when "linking" the static library.     
If that works, great, problem solved  
  
And currently only Boost.Type_index uses that feature and there it isn't even working yet as MSVC seems to be more difficult even though it works with b2. Maybe we can compare the exact compile commands used

---
