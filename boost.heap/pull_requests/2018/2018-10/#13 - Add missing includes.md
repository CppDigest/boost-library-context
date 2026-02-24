# #13 Add missing includes [Closed]

> Username: Lastique  
> Created at: 2018-10-28 17:13:10 UTC  
> Updated at: 2018-10-29 14:29:35 UTC  
> Closed at: 2018-10-29 07:36:32 UTC  
> Url: https://github.com/boostorg/heap/pull/13  

This should fix https://github.com/boostorg/heap/issues/12 and also adds a test for headers being self-contained.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-10-29 14:26:03 UTC  
> Url: https://github.com/boostorg/heap/pull/13#issuecomment-433929880  

We could still have used the Jamfile addition from this one, even though the `mpl/if.hpp` changes no longer apply.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-10-29 14:29:35 UTC  
> Url: https://github.com/boostorg/heap/pull/13#issuecomment-433931223  

You can cherry-pick commit fc8b5744cb308f5039cdf27c4f788bac1700ce96 to only have the test from this PR.

---
