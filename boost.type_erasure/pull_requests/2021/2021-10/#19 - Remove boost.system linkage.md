# #19 Remove boost.system linkage [Merged]

> Username: hdu-sdlzx  
> Created at: 2021-10-05 09:34:52 UTC  
> Updated at: 2021-11-12 14:52:27 UTC  
> Merged at: 2021-11-12 14:52:27 UTC  
> Closed at: 2021-11-12 14:52:27 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/19  

fixes Remove boost.system linkage #18

---

## Comment 1

> Username: hdu-sdlzx  
> Created_at: 2021-11-11 01:50:08 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/19#issuecomment-965918809  

ping @pdimov

---

## Comment 2

> Username: pdimov  
> Created_at: 2021-11-12 05:04:35 UTC  
> Url: https://github.com/boostorg/type_erasure/pull/19#issuecomment-966823209  

This looks good to me, but unfortunately, the timing isn't right; we just released 1.78 beta and this change is too significant for the post-beta changes. It will have to wait for until after 1.78 is released (Dec 8).

---
