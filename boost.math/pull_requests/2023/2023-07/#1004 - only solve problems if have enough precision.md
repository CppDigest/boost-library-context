# #1004 only solve problems if have enough precision [Merged]

> Username: ryanelandt  
> Created at: 2023-07-25 23:31:29 UTC  
> Updated at: 2023-08-05 10:53:57 UTC  
> Merged at: 2023-07-31 14:36:30 UTC  
> Closed at: 2023-07-31 14:36:30 UTC  
> Url: https://github.com/boostorg/math/pull/1004  

This is chunk 3 for #1000   
  
In the `ibeta_inverse.hpp` file, there exists root-finding problems of the form:  
```  
log(x)  + a * log(1 - x) + t  
```  
This function has an extrema at 1 / (1 + a).   
  
**Newton solver finds dubious roots**  
  
For extreme values of a, the extrema evaluates to 0 or 1 due to floating point limitations. For these cases, the objectives function evaluation _always has the same sign, and is never zero_. The Newton solver finds a root for the reasons described in #1005.  
  
**This PR**  
  
- Uses `if (cross == 0 || cross == 1) { return cross; }` to return early for values that lack the numerical precision to construct a meaningful objective function  
- Adds a `BOOST_MATH_ASSERT` to prevent the construction of root finding problems that are problematic.

---

## Comment 1

> Username: mborland  
> Created_at: 2023-07-31 14:36:20 UTC  
> Url: https://github.com/boostorg/math/pull/1004#issuecomment-1658496051  

I'll look into the CI system to see what's going on with the TSAN failures across all of your PRs, but it is clearly unrelated.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2023-08-01 09:22:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1004#pullrequestreview-1556541050  

📁 include/boost/math/special_functions/detail/ibeta_inverse.hpp

```diff
  36 | 
  37 |       T y = 1 - x;
  35 |-       if(y == 0)
```

> Username: jzmaddock  
> Created_at: 2023-08-01 09:22:19 UTC  
> Updated_at: 2023-08-01 09:22:20 UTC  
> Url: https://github.com/boostorg/math/pull/1004#discussion_r1280344739  

I'm not sure we can guarantee that this code is no longer needed?  
  
It's purpose was to return "sane" values that the root finder can still use, even in the case that T has no infinity causing the log functions below to "explode".

> Username: ryanelandt  
> Created_at: 2023-08-01 12:05:49 UTC  
> Updated_at: 2023-08-01 12:06:09 UTC  
> Url: https://github.com/boostorg/math/pull/1004#discussion_r1280528754  

The purpose of this code appeared to be to avoid domain errors in the `log` functions when evaluating `x` values less than 0 or greater than 1. In #1002, the upper Kolmogorov quantile search bound was changed to 1 from `boost::math::tools::max_value<RealType>()` so it doesn't appear to be necessary to be. Removing it doesn't appear to cause test failures.

> Username: jzmaddock  
> Created_at: 2023-08-02 09:46:54 UTC  
> Updated_at: 2023-08-02 09:46:55 UTC  
> Url: https://github.com/boostorg/math/pull/1004#discussion_r1281669936  

Trivial test case is `ibetac_inv(1e50, 10, 1./10)` which is from test_ibeta_inv.hpp:284 and does cause the test to fail here with msvc.

> Username: ryanelandt  
> Created_at: 2023-08-04 21:08:27 UTC  
> Url: https://github.com/boostorg/math/pull/1004#discussion_r1284850329  

This case never makes it to the Newton Solver because as it gets special cased out. Are you sure the branch you are testing is up to date?  
  
https://github.com/boostorg/math/blob/e682c1be3326a7b4ead43ff1c50149a4ce226e2e/include/boost/math/special_functions/detail/ibeta_inverse.hpp#L407-L408

> Username: jzmaddock  
> Created_at: 2023-08-05 10:53:56 UTC  
> Updated_at: 2023-08-05 10:53:57 UTC  
> Url: https://github.com/boostorg/math/pull/1004#discussion_r1285020521  

You're right - my bad!


---
