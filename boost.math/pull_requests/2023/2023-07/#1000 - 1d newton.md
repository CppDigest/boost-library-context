# #1000 1d newton [Open]

> Username: ryanelandt  
> Created at: 2023-07-12 03:44:34 UTC  
> Updated at: 2023-08-05 00:46:40 UTC  
> Url: https://github.com/boostorg/math/pull/1000  

Closes #808   
  
This PR adds Newton-aided bisection (tries both directions) as a fallback if local minimization fails. This provides reasonable protection against bad initial guesses. If a root is bracketed, it provides the guaranteed robustness of `rtsafe`. It also adds the ability to find roots that are difficult to or cannot be bracketed (e.g., cusps). Solver specialization is done without branching and without the need for additional function calls.  
  
The solver makes three attempts to solve find a root. As summarized in the code comment below:  
  
```  
   // Makes three attempts to find a root:  
   //   1. Local minimization starting with the initial x  
   //   2. Bracketing the root in the direction of the initial dx if there is a sign flip  
   //      between f(x) and f(x_bound in direction of dx)  
   //   3. Bracketing the root in the opposite direction of the initial dx if there is a  
   //      sign flip between f(x) and f(x_bound in opposite direction of dx).  
   //  
   // If all three attempts fail, an error is thrown.  
```  
  
The initial local minimization attempt transitions between up to three solvers depending on particular sets of circumstances. The final two attempts use the Case 2B solver (assuming a root is bracketed).  
  
```  
   ////// 1D Newton root finder explanation. //////  
   //  
   // Consider the following three root finding problems:  
   //  
   //   Case 1:  Find a root of the function f(x) given bounds [x_l_orig, x_h_orig] and  
   //            an initial evaluation (x_0, f(x_0), f'(x_0)).  
   //   Case 2U: Find a root of the function f(x) given evaluations at a low point  
   //            (x_l, f(x_l), f'(x_l)) and a high point (x_h, f(x_h), f'(x_h)), where  
   //            and f(x_l) and f(x_h) have the same sign, but have slopes that indicate  
   //            that the function is sloping towards zero between x_l and x_h, forming  
   //            a U shape (hence the name). The U may or may not cross zero.  
   //   Case 2B: Find a root of the function f(x) given evaluations at a low point  
   //            (x_l, f(x_l), f'(x_l)) and a high point (x_h, f(x_h), f'(x_h)), where  
   //            and f(x_l) and f(x_h) have opposite signs that BRACKET a root.  
   //  
   // All three cases operate on similar data, but require different algorithms to  
   // solve. These cases can also transitions between each other. Specifically...  
   //  
   //   Case 1:  The problem will remain in Case 1 as long as dx does not change sign.  
   //            If dx changes sign, the problem will transition to Case 2U or Case 2B.  
   //   Case 2U: The problem will transition to Case 2B if the criteria for Case 2B is  
   //            met, i.e., f(x_l) and f(x_h) have opposite signs.  
   //   Case 2B: Does not transition to other cases.  
   //  
   //  
   ////// Code organization. //////  
   //  
   // The code for the root finding problem is divided into two parts: a state that  
   // contains data and solvers that are dataless. State is stored in the Root1D_State  
   // class. The following solver classes operate on the state:  
   //      - SolverCase1  
   //      - SolverCase2U  
   //      - SolverCase2B  
   // The process of finding a root begins by iterating with the solver for Case 1.  
   // Iterations continue until one of four following things happen:  
   //   - Returns success (f(x) is zero, ... etc)  
   //   - Transition to Case 2U  
   //   - Transition to Case 2B  
   //   - Evaluated limit without being able to transition to Case 2U or Case 2B  
   // Similarly, when iterating in Case 2U, iterations will continue until one of the  
   // following happens:  
   //   - Transition to Case 2B  
   //   - Returns success (f(x) is zero, ... etc)  
   //   - Returns failure (converging to non-zero extrema)  
   // When iterating in Case 2B, iterations will continue until:  
   //    - Returns success (f(x) is zero, ... etc)  
```

---

## Comment 1

> Username: ryanelandt  
> Created_at: 2023-07-12 20:36:32 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1633182221  

The remaining failures seems unrelated to this PR. Let me know if there are any changes I should make.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2023-07-13 14:21:37 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1634335662  

@ryanelandt : Will try to get to this this weekend! First impressions are good though.

---

## Comment 3

> Username: ryanelandt  
> Created_at: 2023-07-13 14:30:55 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1634351809  

@NAThompson Thanks! I look forward to your feedback.

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-07-20 17:38:04 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1644328001  

@NAThompson Any updates on possible changes?

---

## Comment 5

> Username: NAThompson  
> Created_at: 2023-07-20 17:49:40 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1644346430  

@ryanelandt : I'm so sorry! I'm really trying to get to this!  
  
(FYI: I hate it when my PRs don't get reviewed-I feel your pain.)

---

## Review 6 [Commented]

> Username: NAThompson  
> Created_at: 2023-07-22 13:59:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1000#pullrequestreview-1542099447  

📁 include/boost/math/distributions/kolmogorov_smirnov.hpp

```diff
 384 |    return tools::newton_raphson_iterate(detail::kolmogorov_smirnov_quantile_functor<RealType, Policy>(dist, p),
 385 |-            k, RealType(0), boost::math::tools::max_value<RealType>(), get_digits, m);
 385 |+            k, RealType(0), RealType(1), get_digits, m);
```

> Username: NAThompson  
> Created_at: 2023-07-22 13:59:42 UTC  
> Updated_at: 2023-07-22 13:59:43 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1271300105  

What bug does this fix?

> Username: NAThompson  
> Created_at: 2023-07-22 14:01:07 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1271300263  

Oh I see . . . quantile defined on [0,1]. Nice diff.

> Username: NAThompson  
> Created_at: 2023-07-25 16:56:25 UTC  
> Updated_at: 2023-07-25 16:56:26 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1273841621  

@ryanelandt : Could you make this into it's own PR? Then we can split these changes into more digestible chunks.

> Username: ryanelandt  
> Created_at: 2023-07-25 23:36:03 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1274215747  

That's a good idea. I've chunked the ground work into: #1002 #1003 and #1004


---

## Review 7 [Commented]

> Username: NAThompson  
> Created_at: 2023-07-22 14:03:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1000#pullrequestreview-1542099897  

📁 include/boost/math/special_functions/beta.hpp

```diff
1023 |    if(!(boost::math::isfinite)(a))
1024 |-       return policies::raise_domain_error<T>(function, "The argument a to the incomplete beta function must be >= zero (got a=%1%).", a, pol);
1024 |+       return policies::raise_domain_error<T>(function, "The argument a to the incomplete beta function must be finite (got a=%1%).", a, pol);
```

> Username: NAThompson  
> Created_at: 2023-07-22 14:03:58 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1271300645  

Wikipedia states that Re{a}, Re{b} >0 is required: https://en.wikipedia.org/wiki/Beta_function

> Username: ryanelandt  
> Created_at: 2023-07-22 15:49:46 UTC  
> Url: https://github.com/boostorg/math/pull/1000#discussion_r1271311947  

The statement `if (!(boost::math::isfinite)(a))` checks if `a` is finite. I changed the error message to reflect this. The check for `a, b > 0` occurs on either line 1035 or 1055 depending on the value of `normalized`.


---

## Comment 8

> Username: NAThompson  
> Created_at: 2023-07-22 14:09:38 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1646592419  

Approved the CI run, but this is quite a complicated PR. I think @jzmaddock (who wrote the rootfinder) may need to take a look.

---

## Comment 9

> Username: ryanelandt  
> Created_at: 2023-07-22 16:22:35 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1646623073  

Thanks for looking at this @NAThompson. I look forward to what @jzmaddock has to say.

---

## Comment 10

> Username: mborland  
> Created_at: 2023-07-31 14:39:59 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1658503553  

@ryanelandt can you please rebase/pull develop into this now that the other chunks have been merged?

---

## Comment 11

> Username: ryanelandt  
> Created_at: 2023-07-31 19:50:19 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1659036000  

@mborland after doing this I'm getting test failures. These failures are the result of the 1D Newton solver running out of iterations here: https://github.com/boostorg/math/blob/50ef83a47b13c02bf9d6c0e86f9066c431ac42cb/test/test_roots.cpp#L657-L659   
  
Running out of iterations in this test is a bug introduced by the removal of large step protection in #1002. This bug isn't obvious on the `develop` branch because running out of iterations is treated as a success criteria. #1008 describes this issue in greater detail.  
  
I'm trying to figure out the best way to address this for this PR and will update when I figure something out.

---

## Comment 12

> Username: ryanelandt  
> Created_at: 2023-08-05 00:46:39 UTC  
> Url: https://github.com/boostorg/math/pull/1000#issuecomment-1666309722  

@mborland Thanks for your patience. I found a solution to #1008. I made this another chunk for this PR. Please see #1012.

---
