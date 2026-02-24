# #3044 Add basic_fields::contains member function [Merged]

> Username: dvtate  
> Created at: 2025-10-10 20:55:15 UTC  
> Updated at: 2025-10-12 08:38:37 UTC  
> Merged at: 2025-10-12 08:38:36 UTC  
> Closed at: 2025-10-12 08:38:36 UTC  
> Url: https://github.com/boostorg/beast/pull/3044  

Fixes #3039 -- adding a `contains` member function to make `basic_fields` closer to `std::multiset`  
  
Based on [sehe's comment](https://github.com/boostorg/beast/issues/3039#issuecomment-3378710913) I named the function `contains` instead of `exists` but can add an alias if needed. I also replaced library usages of `count` in a boolean context with `contains`. If you'd like I can also do this for the unit tests or revert those changes.

---

## Review 1 [Approved]

> Username: ashtum  
> Created_at: 2025-10-11 06:15:08 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/3044#pullrequestreview-3326706834  

Thank you, this looks perfect.

---
