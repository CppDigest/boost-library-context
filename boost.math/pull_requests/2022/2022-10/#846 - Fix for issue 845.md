# #846 Fix for issue 845 [Merged]

> Username: mborland  
> Created at: 2022-10-16 16:24:22 UTC  
> Updated at: 2022-10-24 11:59:04 UTC  
> Merged at: 2022-10-17 20:52:56 UTC  
> Closed at: 2022-10-17 20:52:56 UTC  
> Url: https://github.com/boostorg/math/pull/846  

Avoid division by zero in special cases  
  
xref: https://github.com/scipy/scipy/issues/17101

---

## Review 1 [Commented]

> Username: WarrenWeckesser  
> Created_at: 2022-10-16 17:05:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/846#pullrequestreview-1143316007  

📁 test/git_issue_845.cpp

```diff
   1 |+ // Copyright Matt Boralnd, 2022
```

> Username: WarrenWeckesser  
> Created_at: 2022-10-16 17:05:42 UTC  
> Updated_at: 2022-10-16 17:05:43 UTC  
> Url: https://github.com/boostorg/math/pull/846#discussion_r996467513  

I see I'm not the only one who sometimes mistypes their own last name. 😄


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2022-10-24 11:59:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/846#pullrequestreview-1153020181  

📁 include/boost/math/distributions/non_central_beta.hpp

```diff
  89 |-                xterm *= (a + i - 1) / (x * (a + b + i - 2));
  89 |+ 
  90 |+                if (a + b + i != 2)
```

> Username: jzmaddock  
> Created_at: 2022-10-24 11:59:03 UTC  
> Updated_at: 2022-10-24 11:59:04 UTC  
> Url: https://github.com/boostorg/math/pull/846#discussion_r1003229397  

Rather than putting an `if` inside the inner loop, it would be better to move calculation of xterm to the start of the loop: albeit it's inconvenient, and we have to be careful to get the algebra right ;)  
  
Again this should wait till after the next release.


---
