# #485 Correctly handle floating-point "equality" in qi/numeric/real.hpp [Closed]

> Username: gpantwrs  
> Created at: 2019-03-18 16:46:32 UTC  
> Updated at: 2019-04-25 13:13:52 UTC  
> Closed at: 2019-04-25 13:13:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/485  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2019-03-18 19:35:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/485#issuecomment-474068731  

I think neither of methods is right. The epsilon in every particular case is a problem specific value. Either the literal parser should be customizable with a comparator, or probably it should just not exist (or fine as-is), because you can do the comparison in a semantic action.

---

## Comment 2

> Username: djowel  
> Created_at: 2019-03-19 05:59:21 UTC  
> Url: https://github.com/boostorg/spirit/pull/485#issuecomment-474208916  

Not sure. Maybe make it customizable; or maybe at least make epsilon customizable with a default.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-04-20 13:23:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/485#issuecomment-485118494  

@gpantwrs would you like to update the PR with a generic solution to the problem?

---

## Comment 4

> Username: djowel  
> Created_at: 2019-04-25 13:13:51 UTC  
> Url: https://github.com/boostorg/spirit/pull/485#issuecomment-486667307  

I'd welcome a generic solution to this. For now, I'll close this and leave the code as-is.

---
