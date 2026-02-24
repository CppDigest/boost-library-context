# #80 Disallow mixed-case long long integer literals [Merged]

> Username: jefftrull  
> Created at: 2020-03-15 20:59:51 UTC  
> Updated at: 2020-03-16 05:54:07 UTC  
> Merged at: 2020-03-16 05:54:02 UTC  
> Closed at: 2020-03-16 05:54:02 UTC  
> Url: https://github.com/boostorg/wave/pull/80  

- Update all four lexers to allow ll/LL but not lL/Ll  
- Add unit tests for checking  
- Remove invalid token from test  
  
If merged this will resolve #58.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-03-15 21:15:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/80#pullrequestreview-374834486  

Thanks!

---
