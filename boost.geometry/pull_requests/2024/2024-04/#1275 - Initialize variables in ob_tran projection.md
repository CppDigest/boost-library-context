# #1275 Initialize variables in ob_tran projection [Merged]

> Username: vissarion  
> Created at: 2024-04-25 09:02:10 UTC  
> Updated at: 2024-07-24 13:45:14 UTC  
> Merged at: 2024-07-24 13:45:10 UTC  
> Closed at: 2024-07-24 13:45:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1275  

This PR intents to prevent a maybe uninitialized warning that appear with recent versions of gcc (11 and 12).

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2024-04-26 17:53:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1275#pullrequestreview-2025566996  

This should work indeed.  
But why don't we initialize it where the variable is declared (the C++11 way)?

---

## Comment 2

> Username: vissarion  
> Created_at: 2024-04-29 12:33:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/1275#issuecomment-2082613943  

> This should work indeed. But why don't we initialize it where the variable is declared (the C++11 way)?  
  
You are right, I was following the old style of the file, now they are initialized where they declared.

---
