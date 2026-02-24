# #144 Extend __has_include() grammar for arbitrary conditional expressions [Merged]

> Username: jefftrull  
> Created at: 2022-01-18 06:32:04 UTC  
> Updated at: 2022-01-18 18:46:17 UTC  
> Merged at: 2022-01-18 18:46:16 UTC  
> Closed at: 2022-01-18 18:46:17 UTC  
> Url: https://github.com/boostorg/wave/pull/144  

Previously the `__has_include()` code assumed it was the last thing on a line. This solves that problem by making the grammar more general.  
  
This also marks the first use of C++11 in the codebase (in the form of a helper lambda)!  
  
If merged this will resolve #143

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-01-18 13:03:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/144#pullrequestreview-855396890  

LGTM, thanks! Nice solution.

---
