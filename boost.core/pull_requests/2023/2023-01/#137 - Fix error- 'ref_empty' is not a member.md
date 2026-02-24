# #137 Fix error: 'ref_empty' is not a member [Closed]

> Username: denzor200  
> Created at: 2023-01-06 18:02:02 UTC  
> Updated at: 2023-01-06 22:17:02 UTC  
> Closed at: 2023-01-06 22:07:44 UTC  
> Url: https://github.com/boostorg/core/pull/137  



---

## Comment 1

> Username: pdimov  
> Created_at: 2023-01-06 18:59:57 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374012700  

This doesn't look like the right fix. But in either case, please first give an example that fails.

---

## Comment 2

> Username: denzor200  
> Created_at: 2023-01-06 19:10:16 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374022179  

> But in either case, please first give an example that fails.  
  
Look at the CI fails in develop

---

## Comment 3

> Username: denzor200  
> Created_at: 2023-01-06 19:12:45 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374024367  

> Look at the CI fails in develop  
  
https://github.com/boostorg/pfr/actions/runs/3857066454/jobs/6574035091  
It also breaks Boost PFR's CI and stops my work on it.

---

## Comment 4

> Username: pdimov  
> Created_at: 2023-01-06 19:18:04 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374028451  

Oh, @Lastique has been doing things. Andrey, please do things on a branch first, before merging to develop.

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-01-06 22:08:37 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374191369  

Oops, sorry for this. I thought I ran the tests locally, but apparently I did so on an intermediate version of the code. Should be fixed now.

---

## Comment 6

> Username: Lastique  
> Created_at: 2023-01-06 22:17:02 UTC  
> Url: https://github.com/boostorg/core/pull/137#issuecomment-1374198415  

> Andrey, please do things on a branch first, before merging to develop.  
  
Ok. Sorry again.

---
