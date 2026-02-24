# #197 Fix applying offset to null pointer [Closed]

> Username: ostash  
> Created at: 2020-04-16 10:10:06 UTC  
> Updated at: 2021-02-27 10:02:11 UTC  
> Closed at: 2020-07-18 18:09:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/197  

This should fix #193.

---

## Comment 1

> Username: ostash  
> Created_at: 2020-04-22 13:26:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/197#issuecomment-617778047  

@robertramey - what do you think? Can this be merged?

---

## Comment 2

> Username: robertramey  
> Created_at: 2020-04-22 14:03:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/197#issuecomment-617798716  

Above it says "All checks have failed".  Why do you think this should be merged?

---

## Comment 3

> Username: ostash  
> Created_at: 2020-04-22 14:25:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/197#issuecomment-617811703  

I will fix checks, but for now, I'd like hear feedback whether this approach looks ok :)

---

## Comment 4

> Username: hajokirchhoff  
> Created_at: 2021-02-27 10:02:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/197#issuecomment-787047724  

Since m_bnext get's initialized to NULL in the constructor, perhaps  
`if (m_bnext != NULL && ++m_bnext<m_bend) {` would be acceptable?

---
