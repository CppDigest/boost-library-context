# #705 Karma: fix off by factor 10 error when rounding to integer [Merged]

> Username: RockinRoel  
> Created at: 2021-11-22 13:20:05 UTC  
> Updated at: 2025-05-09 10:26:56 UTC  
> Merged at: 2021-12-16 22:12:54 UTC  
> Closed at: 2021-12-16 22:12:54 UTC  
> Url: https://github.com/boostorg/spirit/pull/705  

This fixes issue #688. There's some code that divides the integer part  
by 10 and increases dim to compensate for it. This code is not  
applicable to fixed notation, however, since dim is disregarded in that  
case, causing the result to be off by a factor 10.  
  
We can fix this by checking if we're not using fixed notation first,  
before doing the division.  
  
Added a few tests to check for this regression.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2021-11-22 13:25:34 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/705#pullrequestreview-812529988  

LGTM, even if this fixes only part of the issue. It overall improves things.

---

## Comment 2

> Username: RockinRoel  
> Created_at: 2021-11-22 13:40:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/705#issuecomment-975534143  

What part of the issue is not fixed, if I may ask? This part seemed obvious, but I don't have a good insight into everything else the algorithm is doing.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2021-11-22 14:10:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/705#issuecomment-975567509  

> LGTM, even if this fixes only part of the issue. It overall improves things.  
  
The release is in a week, do you think this should be included or merge the PR after the release?  
  
> What part of the issue is not fixed, if I may ask? This part seemed obvious, but I don't have a good insight into everything else the algorithm is doing.  
  
I explained how I understands it in https://github.com/boostorg/spirit/issues/688#issuecomment-975561527.

---

## Comment 4

> Username: hkaiser  
> Created_at: 2021-11-22 14:43:59 UTC  
> Url: https://github.com/boostorg/spirit/pull/705#issuecomment-975599953  

> What part of the issue is not fixed, if I may ask? This part seemed obvious, but I don't have a good insight into everything else the algorithm is doing.  
  
I might misremember things, but I believe that the issue is not specific to the fixed format.

---

## Comment 5

> Username: RockinRoel  
> Created_at: 2021-12-08 08:19:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/705#issuecomment-988594523  

Oh, you mean that it doesn't address issue #628? I indeed only looked at, and am only interested in issue #688. How numerically accurate the output is, is more important than the exact encoding to me.

---
