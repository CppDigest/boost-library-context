# #464 V2: Merge make_attribute into transform_attribute [Merged]

> Username: Kojoley  
> Created at: 2019-02-14 15:30:04 UTC  
> Updated at: 2019-02-18 17:25:22 UTC  
> Merged at: 2019-02-18 17:25:17 UTC  
> Closed at: 2019-02-18 17:25:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/464  

This will allow to simplify transform_attribute (e.g. no need to handle  
references anymore) and to ensure the pre/post/fail calls are made to the  
same transformation.  
  
That's what I mentioned in https://github.com/boostorg/spirit/pull/460#issuecomment-462967855

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2019-02-18 17:09:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/spirit/pull/464#pullrequestreview-204888396  

As long as the tests pass I'm ok with those changes.

---
