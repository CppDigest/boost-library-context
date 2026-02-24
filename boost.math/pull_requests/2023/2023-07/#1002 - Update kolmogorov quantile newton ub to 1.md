# #1002 Update kolmogorov quantile newton ub to 1 [Merged]

> Username: ryanelandt  
> Created at: 2023-07-25 19:32:53 UTC  
> Updated at: 2023-08-01 12:15:29 UTC  
> Merged at: 2023-07-31 14:19:42 UTC  
> Closed at: 2023-07-31 14:19:42 UTC  
> Url: https://github.com/boostorg/math/pull/1002  

This is chunk 1 for #1000  
  
**Change search bound:**  
This PR changes the kolmogorov_smirnov quantile search upper bound from `max_value<RealType>()` to `RealType(1)`. Quantiles appear to be defined on [0,1].  
  
**Simplify Newton Solver**  
The Newton solver previously had to evaluate the kolmogorov functor at `max_value<RealType>() / 2` during bisection, which would cause the program to hang. Newton bisection logic added to prevent this evaluation from happening. With the quantile upper bound changed to 1, this logic is no longer required.

---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2023-07-25 21:00:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1002#pullrequestreview-1546451525  

📁 include/boost/math/tools/roots.hpp

```diff
 284 |-          else
 285 |-             delta = shift;
 278 |+          delta = (delta > 0) ? (result - min) / 2 : (result - max) / 2;
```

> Username: NAThompson  
> Created_at: 2023-07-25 21:00:00 UTC  
> Updated_at: 2023-07-25 21:00:01 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1274115602  

Wait, we may need to protect against huge jumps in *other* people's code right? We only know that this is now unnecessary for Komogorov-Smirnov.

> Username: ryanelandt  
> Created_at: 2023-07-25 22:52:01 UTC  
> Updated_at: 2023-07-26 00:47:16 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1274194413  

I looked into this. Here is the history...  
  
#145 @jzmaddock opened an issue where skew-normal quantiles return non-finite values. This issue occurs because the min/max bounds for the 1D Newton search problem are -Inf/+Inf. Attempting to bisect the search point at either ends gets one stuck at either -Inf/+Inf.  
91c193d2d2c1913f42692795beceb5252c18c09d @jzmaddock Fixed #145 by adding an early version of huge step protection. A test was also added to `test_roots` to prevent possible regressions.  
3ab69d00eefdf8afb1db13a4aecea3a9e1f8f97f The test in `test_roots` was removed as part of adding the complex Newton solver, possibly due to a merge conflict oversight.  
#422 @evanmiller added the kolmogorov-smirnov distribution. The min/max bounds for the 1D Newton search problem are 0/max_value<RealType>(). This new functionality requires huge step protection because evaluating the functor at large values causes the program to hang.

> Username: ryanelandt  
> Created_at: 2023-07-25 22:58:36 UTC  
> Updated_at: 2023-07-25 23:34:40 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1274197605  

Huge step protection seems to cover up issues with Newton search bounds for quantile problems.  
   
This PR attempts to resolve this with the following steps:  
1. Change the `skew_normal` quantile search to go from -max_value/+max_value instead of -Inf/+Inf.  
2. Change the upper search bound of the kolmogorov-smirov distribution to `RealType(1)`. This fixes the hang issue, and causes no test issues, **I don't know if it is right though.**  
3. Add the test that the skew-normal quantile is finite back in

> Username: mborland  
> Created_at: 2023-07-31 14:17:47 UTC  
> Updated_at: 2023-07-31 14:17:48 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1279368090  

I think that changing the bounds to [0, 1] is correct per this table of sample values http://www.matf.bg.ac.rs/p/files/69-[Michael_J_Panik]_Advanced_Statistics_from_an_Elem(b-ok.xyz)-776-779.pdf since it doesn't look like we support D_n,m.

> Username: jzmaddock  
> Created_at: 2023-08-01 09:16:50 UTC  
> Updated_at: 2023-08-01 09:16:51 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1280337884  

Apologies for being late to the party... it'll still be another week at least till I have any time.  
  
Removing the large step protection is probably a bad idea, but I would have to go searching for a test case to tell you why ;)  
  
These root finders are rather problematic to test because they are fundamentally quite resilient even to bugs/errors in their code because of the way they work.  I worry about that, but I also want them to be resilient!

> Username: ryanelandt  
> Created_at: 2023-08-01 12:15:28 UTC  
> Updated_at: 2023-08-01 12:15:29 UTC  
> Url: https://github.com/boostorg/math/pull/1002#discussion_r1280538980  

> Removing the large step protection is probably a bad idea, but I would have to go searching for a test case to tell you why ;)  
  
There's a test case that requires it in the current code-base, although it isn't causing a test failure. Discussed further here: https://github.com/boostorg/math/pull/1000#issuecomment-1659036000.


---
