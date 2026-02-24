# #175 Fix type conversions errors reported by GCC [Merged]

> Username: jplcz  
> Created at: 2024-08-04 04:45:03 UTC  
> Updated at: 2024-09-01 14:22:07 UTC  
> Merged at: 2024-09-01 14:22:07 UTC  
> Closed at: 2024-09-01 14:22:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/175  

This commit fixes errors when compiling with:  
 -Wall -Werror -Wextra -Wconversion -Wsign-conversion

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-08-25 15:56:45 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/175#issuecomment-2308905117  

[![Coverage Status](https://coveralls.io/builds/69414620/badge)](https://coveralls.io/builds/69414620)  
  
coverage: 86.35%. remained the same  
when pulling **e9ce1aebf7b210607ccb8c2a000d77ba88685be2 on jarekpelczar:fix-casts**  
into **cbf0df774dd7baa2af5a7d28662fd58b90ea8c3d on boostorg:develop**.

---
