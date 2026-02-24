# #1242 pass strategy to sort by side [Merged]

> Username: barendgehrels  
> Created at: 2024-02-10 13:43:29 UTC  
> Updated at: 2024-04-01 11:29:32 UTC  
> Merged at: 2024-02-24 09:26:27 UTC  
> Closed at: 2024-02-24 09:26:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1242  

Small PR, passing the strategy i/o the side strategy in several places, which was a TODO already.  
Also documents sort_by_side better.  
It is a preparation step for a next PR.  
  
This should not change anything (apart from users, by chance using `sort_by_side` - but it's all in namespace `detail`, so not considered as a breaking change)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2024-02-24 08:57:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1242#issuecomment-1962303433  

@vissarion is this OK?

---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-02-24 09:04:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1242#pullrequestreview-1899331643  

LGTM, thanks!

---

## Comment 3

> Username: awulkiew  
> Created_at: 2024-02-24 12:35:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1242#issuecomment-1962349398  

A little late but yes, it looks good. Thanks.

---
