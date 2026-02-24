# #149 Use portable filesystem string conversion instead of native() [Merged]

> Username: jefftrull  
> Created at: 2022-02-09 17:39:14 UTC  
> Updated at: 2022-02-09 19:53:31 UTC  
> Merged at: 2022-02-09 19:53:31 UTC  
> Closed at: 2022-02-09 19:53:31 UTC  
> Url: https://github.com/boostorg/wave/pull/149  

VC++ builds fail on this due to the use of wchar_t

---

## Comment 1

> Username: jefftrull  
> Created_at: 2022-02-09 17:40:17 UTC  
> Url: https://github.com/boostorg/wave/pull/149#issuecomment-1034025655  

This was broken in a previous PR but I didn't check MSVC CI results.

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2022-02-09 17:44:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/149#pullrequestreview-877838334  

LGTM, thanks!

---
