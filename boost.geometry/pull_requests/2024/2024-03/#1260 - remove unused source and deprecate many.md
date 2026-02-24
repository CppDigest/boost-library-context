# #1260 remove unused source and deprecate many [Merged]

> Username: barendgehrels  
> Created at: 2024-03-13 17:52:22 UTC  
> Updated at: 2024-03-22 17:52:01 UTC  
> Merged at: 2024-03-22 17:51:55 UTC  
> Closed at: 2024-03-22 17:51:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1260  

This file removes `within_no_turns` because it was never used.  
Then I noticed some files in `detail` that are never used - I think they can also be removed right away.  
  
There are many files deprecated "in the future", I think we should make it more specific now so I made it 1.86  
  
I also noticed the whole `multi` branch which I completely forgot about, it's all empty and very very old, so I also added this deprecation there.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-03-13 18:04:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1260#issuecomment-1995213609  

If still possible, it should make it to 1.85, otherwise we should deprecate it one version later.

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-03-14 09:49:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1260#pullrequestreview-1936190708  

LGTM, thanks!

---
