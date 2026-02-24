# #691 Cease dependence on Foreach [Merged]

> Username: Kojoley  
> Created at: 2021-06-20 13:14:42 UTC  
> Updated at: 2021-06-21 00:35:14 UTC  
> Merged at: 2021-06-20 20:04:35 UTC  
> Closed at: 2021-06-20 20:04:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/691  

I have not touch examples because they are not affecting Spirit usage and testing dependencies, and modernizing them also seems like a waste of time when we encourage to start new projects with X3 instead of Qi.

---

## Comment 1

> Username: djowel  
> Created_at: 2021-06-21 00:17:42 UTC  
> Updated_at: 2021-06-21 00:19:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/691#issuecomment-864635871  

I have not looked deeper, but is there a reason why you not using the form `for(auto& x : c)` which should be a better modern substitute for `BOOST_FOREACH`?   
  
Ah ignore that... I thought this is for X2. Please go ahead...

---

## Comment 2

> Username: Kojoley  
> Created_at: 2021-06-21 00:20:24 UTC  
> Updated_at: 2021-06-21 00:21:03 UTC  
> Url: https://github.com/boostorg/spirit/pull/691#issuecomment-864636485  

Because Qi is still supports C++03. You think we should move on even with V2 Spirit?  
  
Yeah, ignore that. I thought this was for x3.

---

## Comment 3

> Username: djowel  
> Created_at: 2021-06-21 00:21:28 UTC  
> Url: https://github.com/boostorg/spirit/pull/691#issuecomment-864636728  

Why are we moving from `BOOST_FOREACH` BTW, What did I miss?

---

## Comment 4

> Username: Kojoley  
> Created_at: 2021-06-21 00:33:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/691#issuecomment-864640495  

Unjustified additional dependency.

---

## Comment 5

> Username: djowel  
> Created_at: 2021-06-21 00:35:14 UTC  
> Url: https://github.com/boostorg/spirit/pull/691#issuecomment-864640850  

> Unjustified additional dependency.  
  
OK! Agreed.

---
