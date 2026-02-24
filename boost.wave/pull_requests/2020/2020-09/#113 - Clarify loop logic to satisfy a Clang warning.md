# #113 Clarify loop logic to satisfy a Clang warning [Merged]

> Username: jefftrull  
> Created at: 2020-09-25 04:01:00 UTC  
> Updated at: 2020-09-25 14:45:16 UTC  
> Merged at: 2020-09-25 14:45:16 UTC  
> Closed at: 2020-09-25 14:45:16 UTC  
> Url: https://github.com/boostorg/wave/pull/113  

Clang warns that a for loop in a sample increments an iterator in both  
the header and the body. This seems to be done on purpose to skip  
whitespace. I used std::advance in the header instead, for clarity.  
  
Also cleaned up an unused type in the same file while I was there.  
  
If merged, this will resolve #90

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-09-25 13:51:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/113#pullrequestreview-496438176  

LGTM, thanks!

---
