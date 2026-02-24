# #1078 Fix a few warnings [Merged]

> Username: vissarion  
> Created at: 2022-10-24 09:41:24 UTC  
> Updated at: 2022-11-08 08:46:49 UTC  
> Merged at: 2022-11-08 08:46:45 UTC  
> Closed at: 2022-11-08 08:46:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1078  



---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2022-11-01 19:16:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1078#pullrequestreview-1163912403  

📁 include/boost/geometry/util/math.hpp

```diff
 866 |-     remainder -= 90 * quotient;
 863 |+     T remainder = math::mod(x, T(360));
 864 |+     T quotient = std::floor(remainder / T(90) + T(0.5));
```

> Username: awulkiew  
> Created_at: 2022-11-01 19:16:26 UTC  
> Updated_at: 2022-11-01 19:16:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1078#discussion_r1010809489  

Would it make sense to use `const`?


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-11-01 19:17:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1078#pullrequestreview-1163914042  

📁 include/boost/geometry/util/math.hpp

```diff
 871 |-     T s = sin(remainder), c = cos(remainder);
 870 |+     T s = sin(remainder);
 871 |+     T c = cos(remainder);
```

> Username: awulkiew  
> Created_at: 2022-11-01 19:17:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1078#discussion_r1010810494  

Here as well.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-11-08 08:45:27 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1078#pullrequestreview-1171656460  

Thanks!

---
