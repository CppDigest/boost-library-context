# #290 Use std::ref instead of copying the function parameter… [Merged]

> Username: k3DW  
> Created at: 2024-10-03 04:49:34 UTC  
> Updated at: 2024-10-09 00:18:56 UTC  
> Merged at: 2024-10-08 15:35:58 UTC  
> Closed at: 2024-10-08 15:35:58 UTC  
> Url: https://github.com/boostorg/unordered/pull/290  

…in std::initializer_list overloads of insert_{or|and}_[c]visit  
  
Closes #285

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2024-10-04 02:58:02 UTC  
> Url: https://github.com/boostorg/unordered/pull/290#issuecomment-2392700818  

An automated preview of the documentation is available at [https://290.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://290.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---

## Comment 2

> Username: k3DW  
> Created_at: 2024-10-07 02:51:37 UTC  
> Url: https://github.com/boostorg/unordered/pull/290#issuecomment-2395785628  

I've been looking into these test failures, but I can't determine what's going on here. It seems like the failures aren't related to my change, but I'm not certain. I won't merge this myself. I will let @joaquintides do this, to indicate there are no further problems with this PR

---

## Comment 3

> Username: cppalliance-bot  
> Created_at: 2024-10-07 22:42:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/290#issuecomment-2398052642  

An automated preview of the documentation is available at [https://290.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html](https://290.unordered.prtest2.cppalliance.org/libs/unordered/doc/html/unordered.html)

---
