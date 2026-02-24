# #44 Fix #43 (unwanted ADL issues) [Merged]

> Username: morinmorin  
> Created at: 2018-09-22 11:34:02 UTC  
> Updated at: 2018-10-01 12:26:46 UTC  
> Merged at: 2018-09-22 13:05:23 UTC  
> Closed at: 2018-09-22 13:05:23 UTC  
> Url: https://github.com/boostorg/iterator/pull/44  



---

## Comment 1

> Username: Lastique  
> Created_at: 2018-09-22 13:05:36 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-423742847  

Thanks.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-09-22 15:49:45 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-423753261  

@morinmorin I have reverted the change (but not the tests) made by this PR. See 06875a754d6a1e7ed14b761d26020239cf5ad0a2. Feel free to take a look at the problem.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2018-09-23 04:56:34 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-423792005  

I submitted PR to Boost.Range (boostorg/range#75), which moves `distance` into ADL barrier namespace and pulls the name via a using-directive.  
  
However, testing on that PR fails now, since Boost.Range's PR and Boost.Iterator's PR need to be applied in sync.

---

## Comment 4

> Username: Lastique  
> Created_at: 2018-09-23 09:43:08 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-423804089  

I've reapplied your change and also added a test to check compatibility with `boost::distance` from Boost.Range.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-01 11:59:57 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-425881900  

Let me know when this is merged to master so that I can merge Range.

---

## Comment 6

> Username: Lastique  
> Created_at: 2018-10-01 12:26:46 UTC  
> Url: https://github.com/boostorg/iterator/pull/44#issuecomment-425888898  

Just merged it, thanks for the reminder.

---
