# #1224 [ci] Fix mscv versions [Merged]

> Username: vissarion  
> Created at: 2023-12-12 13:17:52 UTC  
> Updated at: 2025-05-20 12:49:40 UTC  
> Merged at: 2025-05-20 12:49:36 UTC  
> Closed at: 2025-05-20 12:49:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1224  

Closes https://github.com/boostorg/geometry/issues/1223

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-02-10 17:43:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1224#pullrequestreview-1873834682  

Looks OK to me

---

## Comment 2

> Username: vissarion  
> Created_at: 2024-02-12 14:20:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1224#issuecomment-1938770072  

@barendgehrels even if it looks OK it isn't. The tests look green but there are the same warnings as before. See https://github.com/boostorg/geometry/issues/1223#issuecomment-1853623770

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2024-02-12 17:21:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1224#issuecomment-1939189272  

> @barendgehrels even if it looks OK it isn't. The tests look green but there are the same warnings as before. See [#1223 (comment)](https://github.com/boostorg/geometry/issues/1223#issuecomment-1853623770)  
  
Okay... I didn't work in this CI yet, so you can ignore my observations.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-05-15 19:51:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1224#issuecomment-2884905447  

hi @vissarion , is this still relevant?

---

## Comment 5

> Username: vissarion  
> Created_at: 2025-05-16 09:28:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1224#issuecomment-2886176094  

OK, it seems there is a way to use `msvc-14.2` (Visual Studio 19) in `windows-2019` runner. The other two available runners `windows-2022` and `windows-2025` or `windows-latest` both support `msvc-14.3` (Visual Studio 22). Finally, there is not choice to run tests on older versions i.e. `msvc-14.1` (Visual Studio 17) because there is no runner in github actions that supports that version.

---
