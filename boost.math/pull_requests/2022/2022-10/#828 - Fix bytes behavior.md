# #828 Fix bytes behavior [Merged]

> Username: SiliconA-Z  
> Created at: 2022-10-03 16:27:50 UTC  
> Updated at: 2023-01-19 17:01:57 UTC  
> Merged at: 2022-10-12 15:27:42 UTC  
> Closed at: 2022-10-12 15:27:42 UTC  
> Url: https://github.com/boostorg/math/pull/828  

"this" is a pointer, not a reference, so we need to find the size of the referenced object.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-10-03 16:34:21 UTC  
> Url: https://github.com/boostorg/math/pull/828#issuecomment-1265726267  

Looks reasonable. I approved the CI run.

---

## Comment 2

> Username: SiliconA-Z  
> Created_at: 2022-10-11 15:54:59 UTC  
> Url: https://github.com/boostorg/math/pull/828#issuecomment-1274921690  

@mborland Could you please test and merge this

---

## Comment 3

> Username: mborland  
> Created_at: 2022-10-11 16:03:50 UTC  
> Url: https://github.com/boostorg/math/pull/828#issuecomment-1274933783  

> @mborland Could you please test and merge this  
  
CI is running; will take a few hours.

---
