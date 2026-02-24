# #473 Explicitly ignore getchar() result [Merged]

> Username: borman  
> Created at: 2026-01-19 12:54:52 UTC  
> Updated at: 2026-01-19 22:19:02 UTC  
> Merged at: 2026-01-19 22:19:02 UTC  
> Closed at: 2026-01-19 22:19:02 UTC  
> Url: https://github.com/boostorg/test/pull/473  

Some toolchains (e.g. recent clang/libcxx) issue "unused result" warnings for getchar.

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2026-01-19 22:18:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/test/pull/473#pullrequestreview-3679767316  

LGTM. Thank you!

---
