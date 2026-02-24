# #976 Fix fisher_f mode [Merged]

> Username: maitbayev  
> Created at: 2023-04-13 12:17:49 UTC  
> Updated at: 2023-04-14 10:30:54 UTC  
> Merged at: 2023-04-14 10:30:54 UTC  
> Closed at: 2023-04-14 10:30:54 UTC  
> Url: https://github.com/boostorg/math/pull/976  

The mode for F-distribution is defined when `df1 > 2` according to https://en.wikipedia.org/wiki/F-distribution. It is also reasonable since `df2 * (df1 - 2) / (df1 * (df2 + 2))` becomes zero or negative when `df1 <= 2`

---

## Review 1 [Commented]

> Username: mborland  
> Created_at: 2023-04-13 12:25:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/976#pullrequestreview-1383295843  

Other than the value the policy is reporting on failure this looks to be correct.

📁 include/boost/math/distributions/fisher_f.hpp

```diff
 311 |       return policies::raise_domain_error<RealType>(
 312 |-          function, "Second degree of freedom was %1% but must be > 2 in order for the distribution to have a mode.", df2, Policy());
 312 |+          function, "First degree of freedom was %1% but must be > 2 in order for the distribution to have a mode.", df2, Policy());
```

> Username: mborland  
> Created_at: 2023-04-13 12:24:55 UTC  
> Updated_at: 2023-04-13 12:25:49 UTC  
> Url: https://github.com/boostorg/math/pull/976#discussion_r1165446732  

```diff  
-         function, "First degree of freedom was %1% but must be > 2 in order for the distribution to have a mode.", df2, Policy());  
+         function, "First degree of freedom was %1% but must be > 2 in order for the distribution to have a mode.", df1, Policy());  
```


---

## Comment 2

> Username: maitbayev  
> Created_at: 2023-04-13 13:57:59 UTC  
> Url: https://github.com/boostorg/math/pull/976#issuecomment-1507014070  

@mborland Good catch. Addressed the comment

---
