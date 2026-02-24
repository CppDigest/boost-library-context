# #142 Fixed modifier bug with karma::left_align [Closed]

> Username: vtnerd  
> Created at: 2015-07-21 00:34:48 UTC  
> Updated at: 2015-07-31 03:33:54 UTC  
> Closed at: 2015-07-21 02:57:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/142  

I found a bug in the factory function for `karma::left_align`. `upper[left_align(10, 'a')['x']]` currently fails to compile because the constructor for the `left_align` object is given a different type. I applied the modifiers to the pad character because `right_align` does, and that appears to be the goal. However, should it be a direct change? In other words should `upper[left_align(10, 'a')['x']]` output `Xaaaaaaaaa` or `XAAAAAAAAA`?

---

## Comment 1

> Username: djowel  
> Created_at: 2015-07-21 00:46:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/142#issuecomment-123111979  

Do we want this in master? If so, please do a PR to develop first, let the tests cycle and I'll do the merge to master.

---

## Comment 2

> Username: hkaiser  
> Created_at: 2015-07-21 00:59:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/142#issuecomment-123114849  

LGTM, this should output `XAAAAAAAAAA`. But it has to go through develop.

---

## Comment 3

> Username: vtnerd  
> Created_at: 2015-07-21 02:57:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/142#issuecomment-123134223  

Sorry about that, the target branch cannot be modified, so I will open a new request. I also added the same tests for `center`, which passed.

---
