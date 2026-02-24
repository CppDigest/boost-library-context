# #4 Add `quick` target to test/Jamfile (used by `b2 quick` in status) [Merged]

> Username: pdimov  
> Created at: 2018-01-14 02:01:35 UTC  
> Updated at: 2018-01-14 16:38:02 UTC  
> Merged at: 2018-01-14 16:00:13 UTC  
> Closed at: 2018-01-14 16:00:13 UTC  
> Url: https://github.com/boostorg/quickbook/pull/4  



---

## Comment 1

> Username: danieljames  
> Created_at: 2018-01-14 15:53:27 UTC  
> Url: https://github.com/boostorg/quickbook/pull/4#issuecomment-357520985  

I think it would be better to run a single test case, such as `quickbook_manual-1_4`, which wouldn't take much longer. Although compiling quickbook is pretty expensive, so just doing that isn't much quicker than a full test run.

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-01-14 16:13:23 UTC  
> Url: https://github.com/boostorg/quickbook/pull/4#issuecomment-357522422  

I don't mind if you change it to whatever you feel is most appropriate. Wanted to have building quickbook as part of the Travis cycle as it's both a critical piece of infrastructure and a good canary.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-01-14 16:38:02 UTC  
> Url: https://github.com/boostorg/quickbook/pull/4#issuecomment-357524065  

Ah you don't support C++03 any longer. Hm.

---
