# #93 Simplify allocator use via core/allocator_access [Merged]

> Username: glenfe  
> Created at: 2020-05-26 13:32:39 UTC  
> Updated at: 2020-05-26 18:09:53 UTC  
> Merged at: 2020-05-26 18:09:52 UTC  
> Closed at: 2020-05-26 18:09:52 UTC  
> Url: https://github.com/boostorg/wave/pull/93  



---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-05-26 13:56:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/93#pullrequestreview-418323621  

LGTM, thanks!

---

## Comment 2

> Username: jefftrull  
> Created_at: 2020-05-26 15:08:32 UTC  
> Url: https://github.com/boostorg/wave/pull/93#issuecomment-634085429  

LGTM too but out of curiosity - what's the motivation for the use of `allocator_XXX_type<A>::type` as opposed to `allocator_traits<A>::XXX_type` as used in the standard?

---

## Comment 3

> Username: glenfe  
> Created_at: 2020-05-26 15:23:32 UTC  
> Url: https://github.com/boostorg/wave/pull/93#issuecomment-634094454  

As we've found in the standards committee, `allocator_traits` as one big collection of traits is mistake. Users specialize it, and then we can't extend it without breaking user specializations. We realize that this (individual traits) is the way we should have had it since the beginning.

---
