# #1353 test: verify failures and remove some rescaling remainings [Merged]

> Username: barendgehrels  
> Created at: 2024-12-07 12:11:57 UTC  
> Updated at: 2025-03-18 13:18:37 UTC  
> Merged at: 2024-12-09 17:22:09 UTC  
> Closed at: 2024-12-09 17:22:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1353  

This PR:  
  
* removes `BoostGeometryWriteExpectedFailures` (it was to compare rescaling with / without)  
* verifies failing tests  
* where not failing anymore, removes the defines or conditions  
  
There were several tests which now succeeds. I didn't bisect them, but it is good to have them enabled now for future PR's.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-07 12:13:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1353#pullrequestreview-2486489024  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
  37 |- // Change compiler defines to constexpr bools
  38 |- // to make conditions more readable
  39 |- // and to always compile all code.
```

> Username: barendgehrels  
> Created_at: 2024-12-07 12:13:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1353#discussion_r1874406821  

This was only done, once now, in this source, so I harmonized it back with the others.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2024-12-07 12:14:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1353#pullrequestreview-2486489218  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 299 |     }
 300 | 
 333 |-     /*** TODO: self-tangencies for difference
```

> Username: barendgehrels  
> Created_at: 2024-12-07 12:14:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1353#discussion_r1874406968  

This TODO was really old and the tests succeed now.  
The 0,1 output is invalid because the input is invalid, and therefore it can be ignored.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2024-12-09 10:11:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1353#pullrequestreview-2488339471  

Thanks!

---
