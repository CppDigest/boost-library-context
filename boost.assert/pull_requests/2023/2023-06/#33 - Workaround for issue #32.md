# #33 Workaround for issue #32 [Closed]

> Username: ricrogz  
> Created at: 2023-06-21 20:40:18 UTC  
> Updated at: 2023-06-22 18:20:23 UTC  
> Closed at: 2023-06-22 15:44:41 UTC  
> Url: https://github.com/boostorg/assert/pull/33  

This fixes #32 by bypassing the problematic macro when building under nvcc.  
  
Given that this is not a critical functionality, it seems acceptable to skip the C++20 source location printer and use a different one.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-06-22 15:15:29 UTC  
> Url: https://github.com/boostorg/assert/pull/33#issuecomment-1602821931  

I'll apply a slight variation of this patch (https://github.com/boostorg/assert/commit/9159dc35251da2c9a80cd3b93685b2f9323e2b81). The differences are cosmetic - workarounds typically go at the end of the `#if`, and the comment has been rephrased.

---
