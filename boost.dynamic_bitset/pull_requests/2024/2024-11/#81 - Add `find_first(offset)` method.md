# #81 Add `find_first(offset)` method [Closed]

> Username: Siapran  
> Created at: 2024-11-18 23:55:26 UTC  
> Updated at: 2024-11-26 15:25:24 UTC  
> Closed at: 2024-11-26 15:25:14 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81  

See #80.

---

## Review 1 [Commented]

> Username: jeking3  
> Created_at: 2024-11-20 12:42:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#pullrequestreview-2448506632  

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1494 |- 
1494 |     const size_type sz = size();
1496 |-     if (pos >= (sz-1) || sz == 0)
```

> Username: jeking3  
> Created_at: 2024-11-20 12:38:12 UTC  
> Updated_at: 2024-11-20 12:42:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#discussion_r1850246542  

The logic for `sz == 0` was eliminated.   Was that intentional?

> Username: Siapran  
> Created_at: 2024-11-20 12:46:59 UTC  
> Updated_at: 2024-11-20 12:47:32 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#discussion_r1850257842  

`sz == 0` was necessary because `sz-1` could underflow. the new check is `pos >= sz`.

---

📁 include/boost/dynamic_bitset/dynamic_bitset.hpp

```diff
1491 | typename dynamic_bitset<Block, Allocator>::size_type
1492 |- dynamic_bitset<Block, Allocator>::find_next(size_type pos) const
1492 |+ dynamic_bitset<Block, Allocator>::find_first(size_type offset) const
```

> Username: jeking3  
> Created_at: 2024-11-20 12:39:28 UTC  
> Updated_at: 2024-11-20 12:42:36 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#discussion_r1850248263  

Renaming the variable led to more deltas than are necessary.

> Username: Siapran  
> Created_at: 2024-11-20 12:51:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#discussion_r1850264123  

the intent was "find the first bit from offset" to contrast "find the first bit after position", but in retrospect, "find the first bit from position" is fine. I'll change that back.


---

## Comment 2

> Username: jeking3  
> Created_at: 2024-11-20 20:52:31 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2489521036  

The CI in this repo might not have run properly for a while - not sure, let's see how the tests go.

---

## Comment 3

> Username: jeking3  
> Created_at: 2024-11-20 22:39:00 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2489678920  

Yuck... all the CI still includes C++03 which most of boost no longer supports.  I bet many of the community maintained repositories are suffering from this...

---

## Comment 4

> Username: Siapran  
> Created_at: 2024-11-22 16:12:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2494123418  

what needs to be done then?

---

## Comment 5

> Username: jeking3  
> Created_at: 2024-11-25 22:05:47 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2499132568  

I fixed the CI today, rebase this PR so we can run the tests again.

---

## Comment 6

> Username: Siapran  
> Created_at: 2024-11-26 14:37:55 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2500975794  

oh god don't tell me I rebased the wrong way round.

---

## Comment 7

> Username: jeking3  
> Created_at: 2024-11-26 15:07:06 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2501064243  

Something doesn't look quite right there...

---

## Comment 8

> Username: Siapran  
> Created_at: 2024-11-26 15:17:00 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2501104867  

...I might just redo this PR, I've no idea what I did wrong with git

---

## Comment 9

> Username: jeking3  
> Created_at: 2024-11-26 15:20:13 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2501120174  

You can typically use `git reflog` to find your code commit(s) before the rebase.

---

## Comment 10

> Username: Siapran  
> Created_at: 2024-11-26 15:24:31 UTC  
> Updated_at: 2024-11-26 15:24:42 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/81#issuecomment-2501140042  

I cherry picked all the commits I made and combined them into [this new branch](https://github.com/Siapran/dynamic_bitset/tree/find-first-pos), there wasn't anything terribly interesting going on in those steps.

---
