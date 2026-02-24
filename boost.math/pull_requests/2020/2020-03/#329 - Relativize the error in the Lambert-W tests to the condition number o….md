# #329 Relativize the error in the Lambert-W tests to the condition number o… [Merged]

> Username: NAThompson  
> Created at: 2020-03-27 12:02:58 UTC  
> Updated at: 2020-03-31 00:27:17 UTC  
> Merged at: 2020-03-31 00:27:12 UTC  
> Closed at: 2020-03-31 00:27:13 UTC  
> Url: https://github.com/boostorg/math/pull/329  

…f function evaluation.  
  
Fixes #328

---

## Comment 1

> Username: pabristow  
> Created_at: 2020-03-27 17:40:22 UTC  
> Url: https://github.com/boostorg/math/pull/329#issuecomment-605147415  

looks sensible (assuming it passes :-) )  
  
It would be useful to document these error bounds in our documentation.  Long ago I tried to do this but failed to get the Corless original info and left it unfinished.  If you can get access to a copy and share it that would be useful.  The Dawson reference is also useful if I haven't referenced already.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-03-27 18:17:09 UTC  
> Url: https://github.com/boostorg/math/pull/329#issuecomment-605190618  

@pabristow : You mean [this one](https://www.uwo.ca/apmaths/faculty/jeffrey/pdfs/W-adv-cm.pdf)?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-03-27 22:23:21 UTC  
> Updated_at: 2020-03-28 16:45:17 UTC  
> Url: https://github.com/boostorg/math/pull/329#issuecomment-605339698  

> It would be useful to document these error bounds in our documentation.   
  
The error bound I used there is a property of every differentiable function evaluated in floating point. Strangely it is not very well known, so it still might be reasonable to document it. In any case there is a [wikipedia entry](https://en.wikipedia.org/wiki/Condition_number#One_variable)!

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-03-29 13:54:55 UTC  
> Url: https://github.com/boostorg/math/pull/329#issuecomment-605639605  

@jzmaddock : It seems I don't have permissions to rerun the failed jobs. Could you either give me permissions (I just got an AppVeyor account) or kick off a rerun?

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-03-29 17:26:53 UTC  
> Url: https://github.com/boostorg/math/pull/329#issuecomment-605670627  

Done both.

---

## Review 6 [Approved]

> Username: pabristow  
> Created_at: 2020-03-30 16:30:44 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/329#pullrequestreview-384028932  

Looks good to me.  I am updating the docs and references to describe this in more detail.

---
