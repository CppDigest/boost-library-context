# #204 Use correct string constructor for non-null-terminated range [Merged]

> Username: jefftrull  
> Created at: 2024-01-28 00:21:57 UTC  
> Updated at: 2024-01-29 02:24:18 UTC  
> Merged at: 2024-01-29 02:24:18 UTC  
> Closed at: 2024-01-29 02:24:18 UTC  
> Url: https://github.com/boostorg/wave/pull/204  

There is no guarantee (indeed, it is unlikely) that data within the scanner buffer will be null terminated and so the constructor that accepts a pointer and a count is appropriate.  
  
This is one of the issues revealed by the `BOOST_WAVE_BSIZE` variation experiment

---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-01-29 00:47:45 UTC  
> Url: https://github.com/boostorg/wave/pull/204#issuecomment-1913788700  

t_5_001 reveals this bug with BOOST_WAVE_BSIZE=43 and the address sanitizer turned on

---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2024-01-29 02:19:53 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/204#pullrequestreview-1847732346  

Excellent catch! LGTM! Thanks.

---
