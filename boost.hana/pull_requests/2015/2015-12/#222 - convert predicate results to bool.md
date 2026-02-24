# #222 [bug] convert predicate results to bool [Closed]

> Username: ricejasonf  
> Created at: 2015-12-14 21:58:37 UTC  
> Updated at: 2015-12-18 17:51:33 UTC  
> Closed at: 2015-12-17 15:16:38 UTC  
> Url: https://github.com/boostorg/hana/pull/222  

Fixes #221

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2015-12-14 22:00:44 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-164573372  

I used `detail::decay` in partition.hpp because `Pred` was `const&`. I noticed in filter.hpp you use `Pred const& pred` with `pred` in the `decltype` which gets around that. Perhaps that is better idk

---

## Comment 2

> Username: ricejasonf  
> Created_at: 2015-12-14 22:08:37 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-164575384  

I would like to know why `partition` was working inside `sort`, but not in my test case. (before I used `detail::decay`)

---

## Comment 3

> Username: ricejasonf  
> Created_at: 2015-12-14 22:11:34 UTC  
> Updated_at: 2015-12-14 22:11:54 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-164576159  

because the predicate was an lvalue?

---

## Comment 4

> Username: ricejasonf  
> Created_at: 2015-12-14 22:37:15 UTC  
> Updated_at: 2015-12-14 22:37:26 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-164582174  

Oh, I think it is because the return value is actually an rvalue because `id` forwards it's return value.

---

## Comment 5

> Username: ldionne  
> Created_at: 2015-12-16 14:18:09 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-165122302  

This looks good to me. Was there anything else you wanted to add to the PR?

---

## Comment 6

> Username: ricejasonf  
> Created_at: 2015-12-16 16:50:47 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-165172428  

No, thank you.

---

## Comment 7

> Username: ricejasonf  
> Created_at: 2015-12-16 16:52:12 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-165172811  

It says "boostorg:master". Can you change that?

---

## Comment 8

> Username: ldionne  
> Created_at: 2015-12-16 16:53:41 UTC  
> Url: https://github.com/boostorg/hana/pull/222#issuecomment-165173220  

Oops, seems like you created a PR against master. I can't change that on GitHub, but I'm going to merge the PR manually anyway.

---
