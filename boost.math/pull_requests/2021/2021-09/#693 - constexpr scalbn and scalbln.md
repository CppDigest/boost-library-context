# #693 constexpr scalbn and scalbln [Merged]

> Username: mborland  
> Created at: 2021-09-18 08:10:20 UTC  
> Updated at: 2021-09-18 17:35:57 UTC  
> Merged at: 2021-09-18 17:12:47 UTC  
> Closed at: 2021-09-18 17:12:47 UTC  
> Url: https://github.com/boostorg/math/pull/693  

Implements: `scalbn`, `scalbnf`, `scalbnl`, `scalbln`, `scalblnf`, and `scalblnl`

---

## Comment 1

> Username: mborland  
> Created_at: 2021-09-18 14:15:34 UTC  
> Url: https://github.com/boostorg/math/pull/693#issuecomment-922282561  

@jzmaddock and @NAThompson this should be good to go. There was a merge conflict I force pushed to resolve (keep the commit log clean), but the CI run was [here](https://github.com/boostorg/math/actions/runs/1248156754), and the only drone error was a killed build for autodiff `USAN` again.

---
