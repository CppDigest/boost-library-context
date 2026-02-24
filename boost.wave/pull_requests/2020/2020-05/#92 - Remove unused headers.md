# #92 Remove unused headers [Merged]

> Username: jefftrull  
> Created at: 2020-05-19 16:36:46 UTC  
> Updated at: 2020-05-19 18:21:10 UTC  
> Merged at: 2020-05-19 18:21:10 UTC  
> Closed at: 2020-05-19 18:21:10 UTC  
> Url: https://github.com/boostorg/wave/pull/92  

Reduce build time by:  
- using iosfwd instead of iostream, where possible  
- where iostream is necessary but only used for debugging,  
  making its inclusion conditional  
- removing old C-style headers  
  
I used IWYU to guide this work

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-05-19 16:41:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/92#pullrequestreview-414632151  

LGTM, thanks!

---
