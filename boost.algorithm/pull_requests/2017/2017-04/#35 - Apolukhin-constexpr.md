# #35 Apolukhin/constexpr [Merged]

> Username: very-cool-name  
> Created at: 2017-04-07 19:38:12 UTC  
> Updated at: 2017-08-29 16:08:02 UTC  
> Merged at: 2017-08-29 16:08:00 UTC  
> Closed at: 2017-08-29 16:08:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35  



---

## Comment 1

> Username: apolukhin  
> Created_at: 2017-04-18 06:59:54 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35#issuecomment-294706680  

https://github.com/boostorg/range/pull/44 was merged to develop, so now it is safe to merge this pull request.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2017-04-30 00:02:26 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35#issuecomment-298202145  

@mclow can you merge it, please? I can't see any broken changes.

---

## Comment 3

> Username: mclow  
> Created_at: 2017-05-17 19:53:55 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35#issuecomment-302213533  

Antony - I like all the constexpr stuff, but adding uses of `std::unary_function` (which has been removed in C++17) is not a direction that I want to go.  I'll play with that bit and suggest an alternative.

---

## Comment 4

> Username: zamazan4ik  
> Created_at: 2017-07-21 18:25:12 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35#issuecomment-317076923  

@mclow What is the status of the PR? Is it ready for merging into develop branch?

---

## Comment 5

> Username: mclow  
> Created_at: 2017-08-29 16:08:02 UTC  
> Url: https://github.com/boostorg/algorithm/pull/35#issuecomment-325713414  

I'm not 100% sure that the constexpr tests are correct, but sure enough to merge.  Please ping me in a week or so to merge to master.

---
