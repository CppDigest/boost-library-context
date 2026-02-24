# #117 Make indentation consistent with code nesting [Merged]

> Username: jefftrull  
> Created at: 2020-10-23 22:08:45 UTC  
> Updated at: 2020-10-24 06:01:05 UTC  
> Merged at: 2020-10-24 06:01:05 UTC  
> Closed at: 2020-10-24 06:01:05 UTC  
> Url: https://github.com/boostorg/wave/pull/117  

These changes, a subset of what clang-format generates, ensure code and comments are aligned properly for the level of the code. There were a number of places where comments (many) and code (some) were four spaces to the left of where they should be. This will make the code easier to read.  
  
One advantage of separating out these changes is they can be verified to be whitespace-only with a simple diff.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2020-10-23 23:18:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/117#pullrequestreview-516068843  

LGTM, thanks!

---
