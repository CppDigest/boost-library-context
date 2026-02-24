# #250 [strategies][cartesian][buffer][join_round] optimize expression [Merged]

> Username: mkaravel  
> Created at: 2015-03-08 18:12:27 UTC  
> Updated at: 2015-03-08 22:05:42 UTC  
> Merged at: 2015-03-08 19:05:13 UTC  
> Closed at: 2015-03-08 19:05:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/250  

calculating number of interior points

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-08 18:45:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/250#issuecomment-77766931  

Looks good - I assume it is unit tested? If so I agree with merging

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-03-08 18:46:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/250#issuecomment-77767015  

Oh now I relook at it, the std::max should be protected as boost recommends - can you have that too? Otherwise I do it later this week. I'm off now

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-08 18:58:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/250#issuecomment-77767627  

`std::max` is now protected (see commit b3b95ae); sorry for that, it slipped.  
  
Yes, unit tests were run, but besides unit tests, you can verify that the two expressions are mathematically equivalent.

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2015-03-08 19:05:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/250#issuecomment-77767978  

That's fast! Thanks!

---
