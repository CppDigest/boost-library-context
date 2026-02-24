# #966 - boost::json::value_to on std::bitset? [Closed]

> Username: infn-ke  
> Created at: 2024-01-03 13:56:22 UTC  
> Updated at: 2024-01-04 15:56:37 UTC  
> Closed at: 2024-01-04 10:54:31 UTC  
> Url: https://github.com/boostorg/json/issues/966  

Does boost json support std::bitset? On boost json 1.82 I cannot use `boost::json::value_to` on a `std::bitset`,  does this require a custom `tag_invoke`?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-01-03 19:10:14 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1875838830  

We don't support it at the moment. But we probably should.

---

## Comment 2

> Username: infn-ke  
> Created at: 2024-01-04 10:54:31 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1876896511  

Ok, thanks for confirming. I added handling of them using ADL and that works for me.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-01-04 12:28:36 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1877019235  

What is your chosen JSON representation of a std::bitset?

---

## Comment 4

> Username: infn-ke  
> Created at: 2024-01-04 12:30:29 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1877021826  

I made `to_ulong` on the bitset. I only had 16 and 32 bitsets.

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-01-04 15:50:32 UTC  
> Updated at: 2024-01-04 15:50:44 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1877326523  

Maybe I should reopen the issue and rename it to "add conversion implementation for std::bitset"?

---

## Comment 6

> Username: infn-ke  
> Created at: 2024-01-04 15:56:37 UTC  
> Url: https://github.com/boostorg/json/issues/966#issuecomment-1877336668  

> Maybe I should reopen the issue and rename it to "add conversion implementation for std::bitset"?  
  
Ok for me if you want.  I guess I would have to remove my `tag_invoke` implementation once boost natively handles bitsets?
