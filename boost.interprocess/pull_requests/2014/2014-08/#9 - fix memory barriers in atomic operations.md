# #9 fix memory barriers in atomic operations [Merged]

> Username: DonOregano  
> Created at: 2014-08-12 08:38:41 UTC  
> Updated at: 2014-09-06 12:17:55 UTC  
> Merged at: 2014-08-14 10:17:52 UTC  
> Closed at: 2014-08-14 10:17:52 UTC  
> Url: https://github.com/boostorg/interprocess/pull/9  

Here are my changes for atomic.hpp, in case you need them for 1.57.   
Otherwise, please disregard the pull request completely...  
  
/Lars

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-08-13 23:03:51 UTC  
> Url: https://github.com/boostorg/interprocess/pull/9#issuecomment-52123716  

Merged, modified ("Use ___sync_synchronize instead of ___atomic as the later is only available in latest builds") and committed to develop branch in commit:  
  
SHA-1: 5a976aa521776ab1cfa339316f772b9552e87e18  
  
Can you test if this works in your systems?

---

## Comment 2

> Username: DonOregano  
> Created_at: 2014-08-14 07:55:01 UTC  
> Url: https://github.com/boostorg/interprocess/pull/9#issuecomment-52153870  

I'll be away on holiday for a week, but when i get back I'll try it out.  
On 14 Aug 2014 01:03, "Ion Gaztañaga" notifications@github.com wrote:  
  
> Merged, modified ("Use ___sync_synchronize instead of ___atomic as the  
> later is only available in latest builds") and committed to develop branch  
> in commit:  
>   
> SHA-1: 5a976aa  
> https://github.com/boostorg/interprocess/commit/5a976aa521776ab1cfa339316f772b9552e87e18  
>   
> Can you test if this works in your systems?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/interprocess/pull/9#issuecomment-52123716.

---

## Comment 3

> Username: DonOregano  
> Created_at: 2014-09-06 12:17:55 UTC  
> Url: https://github.com/boostorg/interprocess/pull/9#issuecomment-54710679  

From what I can tell it works well!

---
