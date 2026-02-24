# #101 Initial set of 1.74 changes [Merged]

> Username: jefftrull  
> Created at: 2020-06-23 17:12:50 UTC  
> Updated at: 2020-06-23 18:59:50 UTC  
> Merged at: 2020-06-23 18:59:50 UTC  
> Closed at: 2020-06-23 18:59:50 UTC  
> Url: https://github.com/boostorg/wave/pull/101  

These have had a good long time to simmer in develop and the `__VA_OPT__` support in particular will be nice to release.  
  
I'm still hoping to have something for `__has_include` prior to tomorrow's feature freeze but just in case it seemed wise to prepare this.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2020-06-23 17:15:36 UTC  
> Url: https://github.com/boostorg/wave/pull/101#issuecomment-648298752  

The conflict seems to have something to do with the allocator fixes, which went into both master and develop. I'll track it down.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2020-06-23 18:03:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/101#pullrequestreview-436029634  

Thanks Jeff for putting this together! It's quite a list of changes, this time.

---

## Comment 3

> Username: jefftrull  
> Created_at: 2020-06-23 18:58:58 UTC  
> Url: https://github.com/boostorg/wave/pull/101#issuecomment-648355262  

Looks like the cleanest fix for the conflict is to directly merge the missing commit from @glenfe, despite the fact it is already in develop...

---
