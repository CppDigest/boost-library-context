# #24 Remove algorithm dep [Merged]

> Username: steveire  
> Created at: 2014-09-10 13:55:16 UTC  
> Updated at: 2014-09-25 00:12:32 UTC  
> Merged at: 2014-09-25 00:12:32 UTC  
> Closed at: 2014-09-25 00:12:32 UTC  
> Url: https://github.com/boostorg/range/pull/24  



---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-09-24 13:40:50 UTC  
> Url: https://github.com/boostorg/range/pull/24#discussion_r17971236  

Why don't these replacement types have guaranteed different sizes?

---

## Comment 2

> Username: theres-waldo  
> Created_at: 2014-09-24 20:03:40 UTC  
> Url: https://github.com/boostorg/range/pull/24#discussion_r17996475  

I think a conforming implementation can have sizeof(double) = 1. (I'm not aware of any implementations like this in practice.)

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-09-24 20:05:19 UTC  
> Url: https://github.com/boostorg/range/pull/24#discussion_r17996586  

I am not aware of them either, but it costs nothing to define these types as `char (&)[1]` and `char (&)[2]`.

---

## Comment 4

> Username: steveire  
> Created_at: 2014-09-24 20:16:16 UTC  
> Url: https://github.com/boostorg/range/pull/24#discussion_r17997256  

I will change it if someone says it's a condition to merge it. Will either of you merge it, or will I still be waiting for someone else?

---
