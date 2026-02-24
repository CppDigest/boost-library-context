# #685 Add missing headers to precise_math.hpp [Merged]

> Username: tinko92  
> Created at: 2020-03-21 20:55:49 UTC  
> Updated at: 2020-03-26 18:58:50 UTC  
> Merged at: 2020-03-26 14:12:32 UTC  
> Closed at: 2020-03-26 14:12:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/685  

I noticed these two missing includes when I tried to use the header on its own.

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-03-21 21:01:19 UTC  
> Updated_at: 2020-03-21 21:01:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/685#issuecomment-602104073  

@tinko92 Could you try adding `alias ext_triangulation` similarly to the ones below and see if this catches the missing headers for you? If it does, then this will also serve as re-runnable confirmation of the fix.  
  
https://github.com/boostorg/geometry/blob/6af84a3a4994ac127c5bfe76799e45f0f83d662c/extensions/test/headers/Jamfile#L47-L50

---

## Comment 2

> Username: tinko92  
> Created_at: 2020-03-21 21:06:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/685#issuecomment-602104633  

It does. Thanks, I was unaware of this testing facility. It also catches other things that I missed in my tests, I will rebase in a couple of minutes.

---

## Comment 3

> Username: tinko92  
> Created_at: 2020-03-21 21:18:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/685#issuecomment-602106062  

The PR now includes the line proposed for the extensions/test/headers/Jamfile and another missing header that was detected through this.  
  
I am not familiar with the other extensions so I am hesitant to open an issue but I noticed that running b2 (in my working copy) in the headers directory produced 17 more failures in total, in extensions_gis, extensions_algorithms and extensions_index. If these are not known false positives, then maybe this is an issue.

---

## Comment 4

> Username: vissarion  
> Created_at: 2020-03-23 11:38:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/685#issuecomment-602540113  

First, I am ok with this PR. Regarding failures in extensions I also have 17. I guess they are related to work in progress or outdated code. @barendgehrels @awulkiew should know more.

---
