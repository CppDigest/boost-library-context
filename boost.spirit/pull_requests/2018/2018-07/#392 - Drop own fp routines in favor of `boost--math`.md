# #392 Drop own fp routines in favor of `boost::math` [Merged]

> Username: Kojoley  
> Created at: 2018-07-27 17:27:42 UTC  
> Updated at: 2018-10-08 10:56:32 UTC  
> Merged at: 2018-07-29 12:40:02 UTC  
> Closed at: 2018-07-29 12:40:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/392  

Remove own fp routines as Boost 1.40 released ~9 years ago.  
The `boost::math::changesign` usage fixes trac 13531.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-07-27 17:33:29 UTC  
> Url: https://github.com/boostorg/spirit/pull/392#issuecomment-408487579  

Actually I do not know what is the purpose of using `math::changesign`. Simple `-n` makes this work perfectly (it correctly changes sign of NaN and Inf values) and compiles into a faster machine code (especially on older compilers and any msvc) https://godbolt.org/g/aVC152

---

## Comment 2

> Username: djowel  
> Created_at: 2018-07-28 00:46:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/392#issuecomment-408570279  

👍 I am for this PR. And yes, -n should be good, even for generic code.

---

## Review 3 [Approved]

> Username: hkaiser  
> Created_at: 2018-07-28 01:34:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/392#pullrequestreview-141303329  

LGTM, thanks!

---

## Comment 4

> Username: hkaiser  
> Created_at: 2018-07-28 01:35:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/392#issuecomment-408573685  

> Actually I do not know what is the purpose of using `math::changesign`.  
  
IIRC, this was an optimization that was much faster for `double`/`float` than `-n`.

---
