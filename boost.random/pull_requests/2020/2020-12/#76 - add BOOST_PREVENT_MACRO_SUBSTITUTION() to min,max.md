# #76 add BOOST_PREVENT_MACRO_SUBSTITUTION() to min,max [Closed]

> Username: kotika  
> Created at: 2020-12-23 20:21:07 UTC  
> Updated at: 2023-05-13 17:14:57 UTC  
> Closed at: 2023-05-13 17:14:56 UTC  
> Url: https://github.com/boostorg/random/pull/76  

Fix https://github.com/boostorg/math/issues/468 which is in turn caused by the collision in minwindef.h on Windows.h

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-01-17 16:36:24 UTC  
> Url: https://github.com/boostorg/random/pull/76#issuecomment-761840665  

@swatanabe: This is breaking a lot of builds throughout the boost project; do you mind taking a look?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-01-26 20:22:18 UTC  
> Url: https://github.com/boostorg/random/pull/76#issuecomment-767804327  

@kotika : Do you understand the merge conflict?

---

## Comment 3

> Username: mborland  
> Created_at: 2021-01-27 19:33:24 UTC  
> Url: https://github.com/boostorg/random/pull/76#issuecomment-768524882  

@kotika: the merge conflict looks to be stemming from your branch of develop missing https://github.com/boostorg/random/commit/f5c6c6bacffcf9c14ff7c492b9af115fe516172b.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-01-28 17:08:37 UTC  
> Url: https://github.com/boostorg/random/pull/76#issuecomment-769232558  

PR #80 encompasses these changes as well as others throughout random.

---

## Comment 5

> Username: swatanabe  
> Created_at: 2023-05-13 17:14:56 UTC  
> Url: https://github.com/boostorg/random/pull/76#issuecomment-1546713346  

This was subsumed by #80 which has been merged

---
