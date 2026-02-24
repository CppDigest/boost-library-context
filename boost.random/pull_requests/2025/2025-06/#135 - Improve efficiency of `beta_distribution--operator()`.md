# #135 Improve efficiency of `beta_distribution::operator()` [Merged]

> Username: mborland  
> Created at: 2025-06-30 19:37:46 UTC  
> Updated at: 2025-07-03 12:56:10 UTC  
> Merged at: 2025-07-02 15:54:39 UTC  
> Closed at: 2025-07-02 15:54:39 UTC  
> Url: https://github.com/boostorg/random/pull/135  

@Hailios since `gamma_distribution::operator()` can be marked `const` we can in fact avoid re-initializing at least 2 gamma distributions for each call to `beta_distribution::operator()`.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-06-30 19:47:20 UTC  
> Updated_at: 2025-07-02 15:54:50 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3020492776  

## [Codecov](https://app.codecov.io/gh/boostorg/random/pull/135?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 95.85%. Comparing base [(`3289126`)](https://app.codecov.io/gh/boostorg/random/commit/3289126cd7cbf03361ea4d5dfbcba6fc42d227b3?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`093554f`)](https://app.codecov.io/gh/boostorg/random/commit/093554f4a7e593b847110c6c7a91c5314919ba97?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/random/pull/135/graphs/tree.svg?width=650&height=150&src=pr&token=7wvW4uAjLp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/random/pull/135?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #135   +/-   ##  
========================================  
  Coverage    95.85%   95.85%             
========================================  
  Files          115      115             
  Lines         8126     8128    +2       
========================================  
+ Hits          7789     7791    +2       
  Misses         337      337             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/random/pull/135?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/random/beta\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/135?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fbeta_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vYmV0YV9kaXN0cmlidXRpb24uaHBw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/random/gamma\_distribution.hpp](https://app.codecov.io/gh/boostorg/random/pull/135?src=pr&el=tree&filepath=include%2Fboost%2Frandom%2Fgamma_distribution.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9yYW5kb20vZ2FtbWFfZGlzdHJpYnV0aW9uLmhwcA==) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/random/pull/135?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/random/pull/135?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [3289126...093554f](https://app.codecov.io/gh/boostorg/random/pull/135?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: Hailios  
> Created_at: 2025-07-02 17:23:55 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3028655922  

Thanks for looking in to this. What does `static` bring here?

---

## Comment 3

> Username: mborland  
> Created_at: 2025-07-02 17:52:44 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3028808823  

> Thanks for looking in to this. What does `static` bring here?  
  
Since the `alpha_dist` and `beta_dist` are both constants we can mark them as `static const` so they're both only initialized once rather than at every function call.

---

## Comment 4

> Username: Hailios  
> Created_at: 2025-07-02 18:49:48 UTC  
> Updated_at: 2025-07-02 18:50:32 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3028990992  

what I mean is that static makes it initialize the gamma distributions truly once, not once per call to the beta dist, or once per beta dist parameters. that is, when the next beta dist arrives, with new parameters, it will get the gamma distributions with the wrong parameters.  
  
here is a small reproducer that showcase the issue. I added prints in the beta dist, see below.  
the expected prints would be   
1 time "alpha 0.500000,beta 1.000000"  
followed by 100 times "alpha 0.100000,beta 1.000000"   
  
but instead all lines become  "alpha 0.500000,beta 1.000000"  
  
```  
TEST(test, reprod2) {  
  boost::random::beta_distribution<double> dist(0.1, 0.1);  
  boost::random::beta_distribution<double> dist2(0.5, 0.5);  
  std::mt19937_64 gen(12345);  
  
  {  
    double Z = dist2(gen);  
    EXPECT_FALSE(std::isnan(Z));  
  }  
  
  for (int i = 0; i < 100; ++i) {  
    double Z = dist(gen);  
    EXPECT_FALSE(std::isnan(Z));  
  }  
}  
```  
  
and in the beta_distribution.hpp, I added prints just to show that the params are wrong  
```  
    /**  
     * Returns a random variate distributed according to the  
     * beta distribution.  
     */  
    template<class URNG>  
    RealType operator()(URNG& urng) const  
    {  
        static const auto alpha_dist = gamma_distribution<RealType>(_alpha, RealType(1.0));  
        static const auto beta_dist = gamma_distribution<RealType>(_beta, RealType(1.0));  
  
        RealType a = 0;  
        RealType b = 0;  
  
        printf("alpha %f,", alpha_dist.alpha());  
        printf("beta %f\n", beta_dist.beta());  
  
        do  
        {  
            a = alpha_dist(urng);  
            b = beta_dist(urng);  
        } while (a + b == RealType(0));  
  
        return a / (a + b);  
    }  
```  
   
 removing `static` makes it work again, and avoids calling .init() on each loop iteration.   
      
 thanks for looking into this.

---

## Comment 5

> Username: mborland  
> Created_at: 2025-07-02 19:02:12 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3029020130  

Oh you're right. I only thought about and plotted the single beta_distribution usage. Will fix promptly.

---

## Comment 6

> Username: mborland  
> Created_at: 2025-07-03 12:56:10 UTC  
> Url: https://github.com/boostorg/random/pull/135#issuecomment-3032169325  

The static has been removed, and all diffs are on master so you'll see them in the 1.89 release in the next few weeks. Thanks again.

---
