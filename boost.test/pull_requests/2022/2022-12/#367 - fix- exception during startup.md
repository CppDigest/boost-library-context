# #367 fix: exception during startup [Closed]

> Username: solosTec  
> Created at: 2022-12-19 10:06:52 UTC  
> Updated at: 2023-06-07 15:03:15 UTC  
> Closed at: 2023-06-07 15:03:15 UTC  
> Url: https://github.com/boostorg/test/pull/367  

On my machine with windows 10 and the latest version of visual studio I get an exception during startup. The reason is that normalize_test_case_name() creates an ill-formed string object. The provided change in line 438 fixes the problem.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-06-07 15:03:15 UTC  
> Url: https://github.com/boostorg/test/pull/367#issuecomment-1581011808  

#374 has been merged with the fix.

---
