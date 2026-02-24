# #62 Avoid warning in `launder_cast` [Merged]

> Username: Flamefire  
> Created at: 2025-11-02 16:07:22 UTC  
> Updated at: 2025-11-02 21:25:09 UTC  
> Merged at: 2025-11-02 20:48:57 UTC  
> Closed at: 2025-11-02 20:48:58 UTC  
> Url: https://github.com/boostorg/move/pull/62  

This fixes a warning for `launder_cast<char*>(ptr)` as it adds `const` in the argument and then C-casts it away which triggers `-Wcast-qual`  
  
I just got a report from a user running into this warning.  
  
I'm using the approach from #61 which looks sane

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2025-11-02 20:49:05 UTC  
> Url: https://github.com/boostorg/move/pull/62#issuecomment-3478309469  

Many thanks!

---
