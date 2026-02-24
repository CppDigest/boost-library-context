# #744 [get_turn_info] Remove the recently added is_ii [Merged]

> Username: barendgehrels  
> Created at: 2020-08-12 09:48:36 UTC  
> Updated at: 2021-06-02 09:47:55 UTC  
> Merged at: 2020-08-19 07:51:31 UTC  
> Closed at: 2020-08-19 07:51:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/744  

I removed the is_ii check, which caused all the mentioned problems, and the `side_of_range` too (I made a local copy because I want to reuse it later).  
To my surprise the case which it was supposed to fix, was fixed anyway, probably by later changes in the same branch. There is one regression in `difference` (without rescaling) but that can/should be solved differently.  
  
These are technical details - I'm still thinking that that situation should be "ii" (`intersection/intersection`) i/o "u/i" (`union/intersection`), but it always worked that way, so I should not change that now.  
  
I tested all the tests and they are all fine (for testing everything, I had to change my environment, because of disk usage problems, but that is fine now).

---

## Review 1 [Approved]

> Username: awulkiew  
> Created_at: 2020-08-12 11:26:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/744#pullrequestreview-465821604  

Thanks!  
So the rest of the change was just refactoring, correct?

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2020-08-12 11:28:38 UTC  
> Updated_at: 2020-08-12 11:29:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/744#issuecomment-672815678  

>   
>   
> Thanks!  
> So the rest of the change was just refactoring, correct?  
  
Many of it, but there is also "handle as touch", but it is only called if rescaling is turned off. So low risk on breaking things currently.  
  
Thanks for your quick review.

---
