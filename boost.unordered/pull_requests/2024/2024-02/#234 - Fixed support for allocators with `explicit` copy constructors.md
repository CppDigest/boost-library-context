# #234 Fixed support for allocators with `explicit` copy constructors [Merged]

> Username: joaquintides  
> Created at: 2024-02-17 08:17:19 UTC  
> Updated at: 2024-02-17 17:58:53 UTC  
> Merged at: 2024-02-17 17:58:48 UTC  
> Closed at: 2024-02-17 17:58:48 UTC  
> Url: https://github.com/boostorg/unordered/pull/234  

Fixes #204

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-02-17 08:28:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949898971  

An automated preview of the documentation is available at [https://234.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://234.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-02-17 08:29:06 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949899185  

Interesting. Are such allocators conforming? The latest allocator requirement table seems to say so; was it always such, or is this a recent change?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-02-17 08:29:31 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949899275  

(Apart from those philosophical questions, I don't see anything wrong with the change.)

---

## Comment 4

> Username: joaquintides  
> Created_at: 2024-02-17 08:43:45 UTC  
> Updated_at: 2024-02-17 08:44:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949903880  

> Interesting. Are such allocators conforming? The latest allocator requirement table seems to say so; was it always such, or is this a recent change?  
  
I don't know for sure. If you refer to the `A a(b)` clause, with `b` being a rebound type from `A`, this seems to have been that way since ever; I couldn't find anything else explicitly allowing implicit convertibility.

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-02-17 08:52:25 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949905767  

Seems intentional because the same type entries explicitly mandate `A a2 = a;` to also work.

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-02-17 09:02:08 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949907876  

Looks like C++03 only had the `X a(b);` requirement, C++11 added the `X a1(a);` requirement, and C++14 added `X a1 = a;`.  
  
This seems a bit accidental to me.

---

## Comment 7

> Username: joaquintides  
> Created_at: 2024-02-17 09:09:39 UTC  
> Updated_at: 2024-02-17 09:10:00 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949909968  

> This seems a bit accidental to me.  
  
Probably. If I recall correctly, though, Chris commented this issue actually hit a real user, so it doesn't hurt to have it fixed.

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-02-17 09:10:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/234#issuecomment-1949910351  

Yeah, I know, I was just wondering whether such allocators are allowed.

---
