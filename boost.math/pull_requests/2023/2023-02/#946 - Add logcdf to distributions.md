# #946 Add logcdf to distributions  [Merged]

> Username: mborland  
> Created at: 2023-02-06 01:34:54 UTC  
> Updated at: 2023-04-13 12:10:39 UTC  
> Merged at: 2023-04-13 12:10:35 UTC  
> Closed at: 2023-04-13 12:10:35 UTC  
> Url: https://github.com/boostorg/math/pull/946  

From #303 and complements #762.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2023-02-06 18:51:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/946#pullrequestreview-1285823436  

📁 include/boost/math/distributions/logistic.hpp

```diff
 189 |+        }
 190 |+ 
 191 |+        return -log(1 + exp(power));
```

> Username: jzmaddock  
> Created_at: 2023-02-06 18:51:47 UTC  
> Url: https://github.com/boostorg/math/pull/946#discussion_r1097790296  

That should probably be `-log1p(exp(power))` to preserve a small valued result when the cdf is ~ 1


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2023-02-06 18:55:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/946#pullrequestreview-1285832917  

📁 include/boost/math/distributions/laplace.hpp

```diff
 266 |+    else
 267 |+    {
 268 |+       result = log(2 - exp((location - x) / scale)) - boost::math::constants::ln_two<RealType>();
```

> Username: jzmaddock  
> Created_at: 2023-02-06 18:55:38 UTC  
> Url: https://github.com/boostorg/math/pull/946#discussion_r1097793686  

`log1p(-exp((location - x) / scale) / 2)` should preserve small valued results better.


---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-02-06 19:09:14 UTC  
> Url: https://github.com/boostorg/math/pull/946#issuecomment-1419603975  

Just checking through to see which others could be usefully specialized,   
  
exponential distribution would be `log1p(- exp(-x * λ) )`  
Geometric: `log1p(-pow(1-p, floor(x)))`  
Logistic: `-log1p(exp(-(x-u)/s))`  
Pareto: `log1p(-pow(beta/x, alpha))`  
Rayleigh: `log1p(-exp(-x^2/2)*sigma^2)`  
Weibull: `log1p(-exp(pow(-x/beta, alpha)))`  
  
I've just typed those straight in, they need careful checking! :)

---

## Comment 4

> Username: mborland  
> Created_at: 2023-02-13 15:36:14 UTC  
> Url: https://github.com/boostorg/math/pull/946#issuecomment-1428153120  

@jzmaddock Now that this is green could you take another look through?

---

## Review 5 [Commented]

> Username: jzmaddock  
> Created_at: 2023-02-13 17:09:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/946#pullrequestreview-1296103683  

📁 include/boost/math/distributions/extreme_value.hpp

```diff
 196 |+       return result;
 197 |+ 
 198 |+    result = -exp((a-x)/b);
```

> Username: jzmaddock  
> Created_at: 2023-02-13 17:09:58 UTC  
> Url: https://github.com/boostorg/math/pull/946#discussion_r1104781342  

Not sure if it matters, but if we're doing complements as well, then this one would be `log1p(-exp(-exp((a-x)/b)))`


---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-02-13 17:30:32 UTC  
> Url: https://github.com/boostorg/math/pull/946#issuecomment-1428362560  

Many thanks Matt - this all looks good to go to me - feel free to merge away.

---

## Comment 7

> Username: mborland  
> Created_at: 2023-02-14 19:57:10 UTC  
> Url: https://github.com/boostorg/math/pull/946#issuecomment-1430299054  

@jzmaddock I added the complement logcdf to the extreme value distribution and the laplace distribution since I missed the latter as well. If you'd like to review those two I can merge once this finishes.

---
