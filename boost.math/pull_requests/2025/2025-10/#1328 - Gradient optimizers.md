# #1328 Gradient optimizers [Merged]

> Username: demroz  
> Created at: 2025-10-17 19:57:55 UTC  
> Updated at: 2026-02-02 14:37:57 UTC  
> Merged at: 2026-02-02 14:37:38 UTC  
> Closed at: 2026-02-02 14:37:38 UTC  
> Url: https://github.com/boostorg/math/pull/1328  

adds a few gradient optimizers  
- gradient descent  
- nesterov  
- and L-BFGS  
  
everything is policy based, so these should be quite extensible. I may add a few more optimziers in the future, but these are the main ones i think. If there are any specific ones that you guys think should be added, I'd be happy to do so. Also although everything is reverse-mode autodiff centric, as long as you provide the objective function, a way to evaluate it, and a way to evaluate the gradient, everything should work correctly.  
  
For some examples on how to use the optimziers: `test_gradient_descent_optimizer.cpp`, `test_nesterov_optimizer.cpp`, and `test_lbfgs.cpp` should be good starting points.  
  
I'm working on the documentation currently. I wanted to hold off to see if any major revisions are necessary

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-10-17 23:34:31 UTC  
> Updated_at: 2026-02-02 14:37:57 UTC  
> Url: https://github.com/boostorg/math/pull/1328#issuecomment-3417512824  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1328?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 95.32%. Comparing base ([`2697069`](https://app.codecov.io/gh/boostorg/math/commit/26970693298662419eb109c8c975dcdbd654da5a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`2a63c30`](https://app.codecov.io/gh/boostorg/math/commit/2a63c305bbbd8ec146b18eac6c9206d4f14ac430?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 106 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1328/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1328?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1328      +/-   ##  
===========================================  
+ Coverage    95.24%   95.32%   +0.07%       
===========================================  
  Files          814      825      +11       
  Lines        69309    67994    -1315       
===========================================  
- Hits         66015    64812    -1203       
+ Misses        3294     3182     -112       
```  
[see 509 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1328/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1328?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1328?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [2697069...2a63c30](https://app.codecov.io/gh/boostorg/math/pull/1328?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: demroz  
> Created_at: 2026-01-29 21:25:45 UTC  
> Url: https://github.com/boostorg/math/pull/1328#issuecomment-3820442445  

At this point the code is something I'm relatively happy with. I also updated the docs. Would be great if this could get reviewed cc: @mborland @NAThompson

---

## Review 3 [Commented]

> Username: mborland  
> Created_at: 2026-01-30 14:20:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1328#pullrequestreview-3728470932  

📁 include/boost/math/optimization/gradient_descent.hpp

```diff
   8 |+ #include <boost/math/optimization/detail/gradient_opt_base.hpp>
   9 |+ #include <boost/math/optimization/detail/rdiff_optimization_policies.hpp>
  10 |+ namespace rdiff = boost::math::differentiation::reverse_mode;
```

> Username: mborland  
> Created_at: 2026-01-30 13:50:57 UTC  
> Updated_at: 2026-01-30 14:20:30 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746406674  

```suggestion  
```  
  
This alias is global, so it will pollute the consumer's environment. Fortunately, you never use it.

---

📁 include/boost/math/optimization/gradient_descent.hpp

```diff
  28 |+   {
  29 |+     // this update effectively "mutes" the tape
  30 |+     // TODO: add a tape scope guard method so that
```

> Username: mborland  
> Created_at: 2026-01-30 13:51:44 UTC  
> Updated_at: 2026-01-30 14:20:30 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746410337  

Is this something that needs to be done before merge?

> Username: demroz  
> Created_at: 2026-01-30 18:21:44 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747453555  

I have since changed my mid. This solution is much cleaner


📁 include/boost/math/optimization/detail/differentiable_opt_utilties.hpp

```diff
  71 |+ /******************************************************************************/
  72 |+ /** @brief simple blas helpers
  73 |+  * may optimize later if benchmarks show its needed, or just switch to Eigen
```

> Username: mborland  
> Created_at: 2026-01-30 13:56:13 UTC  
> Updated_at: 2026-01-30 14:20:30 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746431080  

I think it would be better to decide now whether this will need Eigen support or not. I think adding an external dependency after initial release is not ideal. If need be you could put the Eigen version behind a macro like:  
  
`BOOST_MATH_OPTIMIZATION_USE_EIGEN`  
  
Which will allow users to decide if they want to use Eigen, or just rely on the STL. I'm not sure how much extra work that would be.

> Username: demroz  
> Created_at: 2026-01-30 18:02:59 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747391519  

I think it would be better to keep this as a standalone library. After thinking about this a bit more, `Eigen` would really only be useful if we ever decide to add newton-based optimizers with explicit Hessian calculations, but even then I think rolling some small LA routines is probably sufficient

---

📁 include/boost/math/optimization/detail/differentiable_opt_utilties.hpp

```diff
 154 |+    */
 155 |+   static boost::random::mt19937 rng{ std::random_device{}() };
 156 |+   static boost::random::uniform_real_distribution<RealType> dist(0.0, 1.0);
```

> Username: mborland  
> Created_at: 2026-01-30 13:57:49 UTC  
> Updated_at: 2026-01-30 14:20:30 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746440094  

Is Boost.Math required here instead of using `<random>`? I ask because this would break the ability to be used as part of our standalone package.

> Username: demroz  
> Created_at: 2026-01-30 19:14:35 UTC  
> Updated_at: 2026-01-30 19:18:16 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747623497  

It could be changed to use `<random>` instead, but for `multiprecision` types `std::uniform_real_distribution<RealType>` throws compiler errors. In practice, we could cast to it `double` and cast it back to `multiprecision`. I'm not sure what's best here.

> Username: mborland  
> Created_at: 2026-01-30 19:31:46 UTC  
> Updated_at: 2026-01-30 19:31:47 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747672422  

I think it's best to generate a double and then cast it to a multiprecision type. I don't think the lost precision would really matter in this case.

---

📁 include/boost/math/optimization/detail/differentiable_opt_utilties.hpp

```diff
 148 |+    */
 149 |+ 
 150 |+   /** TODO: these may need to be marked thread local
```

> Username: mborland  
> Created_at: 2026-01-30 13:59:27 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746450790  

Does this need to be completed pre-merge? It will may cause you issues with MinGW if you make this change. See: https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L657

> Username: demroz  
> Created_at: 2026-01-30 18:04:55 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747396720  

no, this is fine. I'll remove the comment


📁 include/boost/math/optimization/detail/gradient_opt_base.hpp

```diff
   3 |+ //      (See accompanying file LICENSE_1_0.txt or copy at
   4 |+ //           https://www.boost.org/LICENSE_1_0.txt)
   5 |+ #ifndef GRADIENT_OPT_BASE_HPP
```

> Username: mborland  
> Created_at: 2026-01-30 14:00:47 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746458763  

```diff  
-#ifndef GRADIENT_OPT_BASE_HPP  
+#ifndef BOOST_MATH_OPTIMIZATION_DETAIL_GRADIENT_OPT_BASE_HPP  
```  
  
Similar suggestion applies to the other files for consistency


📁 include/boost/math/optimization/detail/rdiff_optimization_policies.hpp

```diff
 103 |+   {
 104 |+     boost::random::mt19937 gen(seed_);
 105 |+     boost::random::uniform_real_distribution<RealType> dist(low_, high_);
```

> Username: mborland  
> Created_at: 2026-01-30 14:03:10 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746472354  

Same question about the use of boost random


📁 include/boost/math/optimization/lbfgs.hpp

```diff
  10 |+ #include <vector>
  11 |+ 
  12 |+ #include "boost/math/optimization/detail/line_search_policies.hpp"
```

> Username: mborland  
> Created_at: 2026-01-30 14:03:56 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746475543  

```diff  
-#include "boost/math/optimization/detail/line_search_policies.hpp"  
+#include <boost/math/optimization/detail/line_search_policies.hpp>  
```

---

📁 include/boost/math/optimization/lbfgs.hpp

```diff
  58 |+       g_prev.assign(g_k.begin(), g_k.end());
  59 |+       x_prev.resize(x.size());
  60 |+       std::transform(x.begin(), x.end(), x_prev.begin(), [](const auto& xi) {
```

> Username: mborland  
> Created_at: 2026-01-30 14:07:42 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746490207  

Would it be worth adding support for C++17 execution policies? This would allow the user to pass say `std::execution::par` and then you could call `std::transform(user_execution_policy, x.begin(), ...)`.

> Username: demroz  
> Created_at: 2026-01-30 19:00:50 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747578149  

It’s possible this could provide a small speedup, but in practice the impact here is likely to be modest since the affected operations are simple, memory-bound transforms and typically not the dominant cost of an L-BFGS iteration.  
  
Supporting C++17 execution policies would also require additional feature-detection macros and conditional code paths to preserve the C++14 baseline that Boost.Math requires.  
  
I think this would be better handled as a separate PR with targeted benchmarks to justify the added complexity.

> Username: mborland  
> Created_at: 2026-01-30 19:32:44 UTC  
> Updated_at: 2026-01-30 19:32:45 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747675021  

Sounds good. C++17 execution policies can be a pain to support since some require linking to TBB, some provide only the header or half-baked impl, etc. etc.

---

📁 include/boost/math/optimization/lbfgs.hpp

```diff
 214 |+       const RealType yTr = dot(state_.Y[i], r);
 215 |+       const RealType beta = state_.rho[i] * yTr;
 216 |+       axpy(alpha[i] - beta, state_.S[i], r);
```

> Username: mborland  
> Created_at: 2026-01-30 14:09:43 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746499534  

On the execution policy question I believe loops like this could be replaced with `std::for_each` since the iterations all seem separable.


📁 include/boost/math/optimization/minimizer.hpp

```diff
   7 |+ #include <boost/math/optimization/detail/differentiable_opt_utilties.hpp>
   8 |+ #include <boost/math/optimization/gradient_optimizers.hpp>
   9 |+ #include <vector>
```

> Username: mborland  
> Created_at: 2026-01-30 14:12:23 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746509626  

```diff  
-#include <vector>  
+#include <vector>  
+#include <chrono>  
```


📁 doc/optimization/gradient_optimizers.qbk

```diff
 704 |+ * `ObjectiveEvalPolicy&& oep` : policy for evaluating the objective function value at a given x. By default this is a reverse-mode AD evaluation policy when using `rvar`.
 705 |+ * `GradEvalPolicy&& gep` : policy for evaluating the gradient of the objective. By default this is a reverse-mode AD gradient evaluation policy when using `rvar`.
 706 |+ * `LineSearchPolicy&& lsp` : policy for selecting the step length alpha. Default is Strong Wolfe, but Armijo is an option.
```

> Username: mborland  
> Created_at: 2026-01-30 14:18:29 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746532421  

Maybe explain what the differences are between these policies

> Username: demroz  
> Created_at: 2026-01-30 17:37:18 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747304512  

Added a short explanation

---

📁 doc/optimization/gradient_optimizers.qbk

```diff
 416 |+ * `RealType lr` : learning rate. Larger values take larger steps (faster but potentially unsable). Smaller values are more stable but converge more slowly.
 417 |+ * `RealType mu` : momentum coefficient in `[0,1)`. Higher values, e.g. 0.9 to 0.99, typically accelerate convergence but may require a smaller `lr`
 418 |+ * `InitializationPolicy&& ip` : initialization policy for the optimizer state and variables. For NAG, this also initializes the internal momentum/velocity state. By default the optimizer uses the same initializer as gradient descent and initializes velocity to zero.
```

> Username: mborland  
> Created_at: 2026-01-30 14:19:03 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746535376  

Can the user adjust the initialization policy? If so how, and why would they want to?

> Username: demroz  
> Created_at: 2026-01-30 17:45:37 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2747331370  

Yes. There are a few initialization policies I provided in `optimization/detal/rdiff_optimization_policies.hpp` for `rvar` specifically. By default the user just provides a starting point by initializing the values of `rvar`, and the initialization policy just controls how the `tape` is initialized. There are a few policies like `random_uniform_initializer_rvar` which initializes the `rvar` container with random values. A bit more complicated of a usecase is if the user doesnt want to use the boost autodiff library and provides their own AD variable, or supplies the gradient, in which case they'd need a custom init policy.

---

📁 doc/optimization/gradient_optimizers.qbk

```diff
1250 |+ 
1251 |+ This policy initializes each element of x to the constant value v
1252 |+ 
```

> Username: mborland  
> Created_at: 2026-01-30 14:20:17 UTC  
> Updated_at: 2026-01-30 14:20:31 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2746543001  

It may be worth adding a table at the end summarizing the different optimizers, policies, and when people should reach for each of the different options.

> Username: demroz  
> Created_at: 2026-01-31 00:48:32 UTC  
> Url: https://github.com/boostorg/math/pull/1328#discussion_r2748600517  

added


---

## Comment 4

> Username: mborland  
> Created_at: 2026-01-30 14:22:52 UTC  
> Url: https://github.com/boostorg/math/pull/1328#issuecomment-3824032928  

@demroz in general this looks very good! Left some comments, and opinions that you can feel free to push back on. I pushed changes to CI and spelling rather than leaving those as TODOs for you.

---

## Review 5 [Commented]

> Username: demroz  
> Created_at: 2026-01-30 19:27:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/1328#pullrequestreview-3730008936  

@mborland   
  
I updated the PR according to your suggestions. A few points :   
  
1.  I think adding support for `C++17` execution policies should be done in a separate PR with dedicated benchmarks.   
2.  using `<random>` instead of `<boost/random.hpp>`.  I'm not sure what should be done here. I agree with your point that this breaks the ability of this being a standalone package. However, `<random>` doesn't support multiprecision types. We could `static_cast` to `double` for multiprecision types, and `static_cast` back to `multiprecision` but thats not behavior I would expect as a user. Practically, i doubt it makes much difference as this is just an initialization policy. Any thoughts?

---

## Comment 6

> Username: mborland  
> Created_at: 2026-01-30 19:34:41 UTC  
> Url: https://github.com/boostorg/math/pull/1328#issuecomment-3825385454  

> @mborland  
>   
> I updated the PR according to your suggestions. A few points :  
>   
> 1. I think adding support for `C++17` execution policies should be done in a separate PR with dedicated benchmarks.  
> 2. using `<random>` instead of `<boost/random.hpp>`.  I'm not sure what should be done here. I agree with your point that this breaks the ability of this being a standalone package. However, `<random>` doesn't support multiprecision types. We could `static_cast` to `double` for multiprecision types, and `static_cast` back to `multiprecision` but thats not behavior I would expect as a user. Practically, i doubt it makes much difference as this is just an initialization policy. Any thoughts?  
  
I mentioned it above but I think generating a random double and then casting it to a multiprecision type should be fine. I would not expect the lost digits to create much of a difference.

---

## Comment 7

> Username: demroz  
> Created_at: 2026-01-31 00:49:40 UTC  
> Url: https://github.com/boostorg/math/pull/1328#issuecomment-3826883440  

@mborland   
  
I updated the docs and removed the `boost::random` dependency. Let me know if anything else is needed from my end to merge

---

## Review 8 [Approved]

> Username: mborland  
> Created_at: 2026-02-02 14:37:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1328#pullrequestreview-3739819961  

Looks good to me. Thanks for this!

---
