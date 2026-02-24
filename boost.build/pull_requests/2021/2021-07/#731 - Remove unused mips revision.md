# #731 Remove unused mips revision [Closed]

> Username: wzssyqa  
> Created at: 2021-07-22 07:33:09 UTC  
> Updated at: 2021-10-02 20:59:15 UTC  
> Closed at: 2021-07-26 02:49:46 UTC  
> Url: https://github.com/boostorg/build/pull/731  

There are several mips revision:  
  'mips1', 'mips2', 'mips3', 'mips4', 'mips32', 'mips32r2', 'mips64'.  
while in fact only mips1 is used, so remove other ones.  
And, mips1 in fact is used for all MIPS revisions.  
  
Then rename mips1 to mips.

---

## Comment 1

> Username: wzssyqa  
> Created_at: 2021-07-22 07:45:37 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-884717865  

https://github.com/boostorg/context/pull/93  
is sub-PR of this PR

---

## Comment 2

> Username: wzssyqa  
> Created_at: 2021-07-22 07:59:56 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-884724990  

https://github.com/boostorg/log/pull/155  
is sub-PR for `log`.

---

## Comment 3

> Username: wzssyqa  
> Created_at: 2021-07-22 08:09:03 UTC  
> Updated_at: 2021-07-22 08:19:45 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-884730145  

https://github.com/boostorg/config/pull/254  
is the sub-PR for `config`.

---

## Comment 4

> Username: wzssyqa  
> Created_at: 2021-07-22 08:14:38 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-884733412  

https://github.com/boostorg/boost/pull/540  
is the sub-PR for `boost`.

---

## Review 5 [Commented]

> Username: Kojoley  
> Created_at: 2021-07-24 13:36:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/731#pullrequestreview-714234962  

You should not remove `mips1` straight away if there is a code that depends on it (is not it an obvious thing?). Why not simply add `mips` without removing `mips*`?  
  
Also, B2 development has moved to https://github.com/bfgroup/b2. You need to open PR there.

---

## Comment 6

> Username: wzssyqa  
> Created_at: 2021-07-26 02:49:46 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-886331090  

@Kojoley thanks for your instruction: https://github.com/bfgroup/b2/pull/58

---

## Comment 7

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:15 UTC  
> Url: https://github.com/boostorg/build/pull/731#issuecomment-932819611  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
