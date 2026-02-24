# #348 Make float128 quiet_NaN constexpr [Merged]

> Username: mborland  
> Created at: 2021-07-25 12:25:23 UTC  
> Updated at: 2021-07-25 18:41:35 UTC  
> Merged at: 2021-07-25 17:56:33 UTC  
> Closed at: 2021-07-25 17:56:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/348  

Constructing from a `const char*` is not constexpr; replace with `NAN`.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-07-25 17:16:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/348#issuecomment-886232299  

The only CI failure is from a killed build.

---
