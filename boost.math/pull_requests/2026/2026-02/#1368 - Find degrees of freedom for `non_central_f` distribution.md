# #1368 Find degrees of freedom for `non_central_f` distribution [Open]

> Username: JacobHass8  
> Created at: 2026-02-21 01:16:51 UTC  
> Updated at: 2026-02-23 19:59:41 UTC  
> Url: https://github.com/boostorg/math/pull/1368  

Towards #1305. I've added functions to find parameters `df1` and `df2` because they are nearly identical.   
  
All the tests are passing, but changing the initial guess for the degrees of freedom that is passed to `bracket_and_solve` causes most tests to fail. Thus, I'm not super confident in the current implementation. I will add more spot checks for a wider range of `df1`/`df2` to make sure things don't break. Perhaps the initial guess should change based on if the different of `p - cdf` is positive or negative? For now, I'm going to leave it though.   
  
I also had some difficulty with seeing if the function passed to `bracket_and_solve` was rising or falling. I ultimately found that the following worked   
```C++  
RealType guess = 1; // Changing this to 20, 200 causes errors!   
std::pair<RealType, RealType> ir = tools::bracket_and_solve_root(  
   f, guess, RealType(2), f(guess) < 0 ? true : false, tol, max_iter, pol);  
```  
where   
```C++  
f_degrees_of_freedom_finder<RealType, Policy> f(x, v, nc, find_v1, p < q ? p : q, p < q ? false : true);  
```  
Essentially, if the difference between `cdf-p` (or is negative then `f` is increasing, and if it is positive then `f` is decreasing. I'm not entirely sure why this works though.   
  
Could all of this rising/falling be avoided by minimizing the squared difference `(cdf-p)^2`? Maybe this will open up a whole new can of worms though.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2026-02-21 02:34:35 UTC  
> Updated_at: 2026-02-21 18:18:26 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3937971437  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `96.92308%` with `2 lines` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 95.34%. Comparing base ([`a10f3c8`](https://app.codecov.io/gh/boostorg/math/commit/a10f3c86fc3d3dc87bb8c7e08acbf312096c9f59?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`3eb7561`](https://app.codecov.io/gh/boostorg/math/commit/3eb7561296c5a15cc2c891a68e5c7f7a624d31e4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [include/boost/math/distributions/non\_central\_f.hpp](https://app.codecov.io/gh/boostorg/math/pull/1368?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_f.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfZi5ocHA=) | 96.72% | [2 Missing :warning: ](https://app.codecov.io/gh/boostorg/math/pull/1368?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1368/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1368?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1368      +/-   ##  
===========================================  
- Coverage    95.34%   95.34%   -0.01%       
===========================================  
  Files          825      825                
  Lines        68160    68224      +64       
===========================================  
+ Hits         64987    65048      +61       
- Misses        3173     3176       +3       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_nc\_f.cpp](https://app.codecov.io/gh/boostorg/math/pull/1368?src=pr&el=tree&filepath=test%2Ftest_nc_f.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X25jX2YuY3Bw) | `100.00% <100.00%> (ø)` | |  
| [include/boost/math/distributions/non\_central\_f.hpp](https://app.codecov.io/gh/boostorg/math/pull/1368?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Fdistributions%2Fnon_central_f.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL2Rpc3RyaWJ1dGlvbnMvbm9uX2NlbnRyYWxfZi5ocHA=) | `89.70% <96.72%> (+1.73%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1368/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a10f3c8...3eb7561](https://app.codecov.io/gh/boostorg/math/pull/1368?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2026-02-21 12:52:40 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3938735802  

I looked into the case:  
  
```  
   double v1 = 5;  
   double v2 = 2;  
   double nc = 1;  
   double x = 1.5;  
   double P = 0.49845842011686358665786775091245664;  
```  
  
And trying to find v1, plotting the output of the function we're trying to root find on yields:  
  
<img width="1000" height="600" alt="Code_Generated_Image(1)" src="https://github.com/user-attachments/assets/749e0360-76ef-4bdf-a590-859b3594b60a" />  
  
So we have two roots, and it's not clear to me that we can reliably determine which way the function slopes?  
  
One thing I thought of was 1) determine if there is a root in [1, INF), if yes, then find it, otherwise 2) determine if there is a root in (0, 1], if yes then find it, otherwise throw.  
  
But it's not clear that this will always work, or if 1 is the correct pivot point in all cases.

---

## Comment 3

> Username: JacobHass8  
> Created_at: 2026-02-21 21:37:52 UTC  
> Updated_at: 2026-02-21 21:43:40 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3939547072  

From sparse testing, it seems like there are two roots when $x < \nu_2, \nu_1$. See the plots below. Sorry it's a bit of a data dump.   
  
This is just a rough estimate. I can't figure out where the point of inflection is.   
  
<details><summary>Details</summary>  
<p>  
  
<img width="640" height="480" alt="Figure_1" src="https://github.com/user-attachments/assets/9004ff69-2a6b-40a8-a797-dc9b1067f7ef" />  
<img width="640" height="480" alt="Figure_2" src="https://github.com/user-attachments/assets/2ac8eb58-6a43-4fc4-bd56-4a0ad0bd4c13" />  
<img width="640" height="480" alt="Figure_3" src="https://github.com/user-attachments/assets/ac8a73ec-37a4-4b12-85b2-d577f39402ef" />  
<img width="640" height="480" alt="Figure_4" src="https://github.com/user-attachments/assets/3bd21bcf-0978-48a7-8c86-c1cfd6095a71" />  
<img width="640" height="480" alt="Figure_5" src="https://github.com/user-attachments/assets/a48067cf-0c0c-44d5-ad4f-796a40cad73a" />  
<img width="640" height="480" alt="Figure_6" src="https://github.com/user-attachments/assets/5643acf2-4608-4832-93ae-d332f8b3eca0" />  
  
</p>  
</details>

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2026-02-22 18:17:40 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3941476635  

>From sparse testing, it seems like there are two roots when x < ν 2 , ν 1 .  
  
Close, but:  
  
   double v1 = 5;  
   double v2 = 3.5;  
   double nc = 0;  
   double x = 3.5000000001;  
  
Has two roots and x > v2.  
  
On the other hand as the parameters get larger, the likelyhood of two roots seems to deminish regardless of the value of x.

---

## Comment 5

> Username: JacobHass8  
> Created_at: 2026-02-22 19:30:41 UTC  
> Updated_at: 2026-02-22 19:32:26 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3941576427  

> > From sparse testing, it seems like there are two roots when x < ν 2 , ν 1 .  
>   
> Close, but:  
>   
> double v1 = 5; double v2 = 3.5; double nc = 0; double x = 3.5000000001;  
>   
> Has two roots and x > v2.  
  
You're right! Even going to x=3.51 there are two roots.

---

## Comment 6

> Username: JacobHass8  
> Created_at: 2026-02-22 19:58:11 UTC  
> Updated_at: 2026-02-22 20:12:18 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3941614605  

Aha, scipy makes a note that it arbitrarily finds a root since the cdf is not necessarily monotonic. See [here](https://docs.scipy.org/doc/scipy/reference/generated/scipy.special.ncfdtridfd.html#scipy.special.ncfdtridfd).  
  
Actually, it's just breaking for cases where there are two roots.   
  
<details><summary>Details</summary>  
<p>  
  
```python  
import numpy as np  
from matplotlib import pyplot as plt  
from scipy.stats import ncf  
from scipy.special import ncfdtridfd, ncfdtr, ncfdtridfn  
  
if __name__ == '__main__':  
    dfn = 5  
    x = 3.51  
    dfd = 3.5  
    nc = 0  
    p = ncfdtr(dfn, dfd, nc, x)  
      
    print(ncfdtridfn(p, dfd, nc, x)) # 1e+100  
  
    df1 = np.linspace(0, 10, num=1000)  
    cdf1 = ncf.cdf(x, df1, dfd, nc)  
  
    fig, ax = plt.subplots()  
    ax.plot(df1, -(p-cdf1))  
    ax.hlines(0, 0, 10, ls='--', color='k')  
    ax.set_xlabel('df1')  
    ax.set_ylabel("cdf-p")  
    plt.show()  
```  
<img width="640" height="480" alt="Figure_1" src="https://github.com/user-attachments/assets/76f52cb2-6801-4003-8b0c-425735370c50" />  
  
  
</p>  
</details>

---

## Comment 7

> Username: dschmitz89  
> Created_at: 2026-02-23 07:06:44 UTC  
> Updated_at: 2026-02-23 07:27:48 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3943047501  

Yikes, I was afraid we would encounter something like this. These functions were never tested regularly in SciPy and are likely pretty much unused in the wild, that's why we never got any feedback about it.  
  
One could give users the possibility to choose between the roots (with default to `higher` e. g.).  
  
The more important issue for me is though if these inverses for the degrees of freedom are actually useful and helpful. I could open a RfC in SciPy to discuss a possible deprecation. Are there statistical applications for these parameter finders? I only know that for example `statsmodels` uses the inverse with respect to the noncentrality which is well behaved unlike the ones we are looking at here.  
  
CC @steppi @mdhaber : do you see a strong reason/use case to keep parameters finders with regard to the degrees of freedom for the F distributions around? They have two [two possible solutions](https://github.com/boostorg/math/pull/1368#issuecomment-3938735802).

---

## Comment 8

> Username: mdhaber  
> Created_at: 2026-02-23 15:57:29 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3945661016  

Hmm, no, I'm not familiar with use cases.

---

## Comment 9

> Username: JacobHass8  
> Created_at: 2026-02-23 16:55:48 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3945998773  

> The more important issue for me is though if these inverses for the degrees of freedom are actually useful and helpful. I could open a RfC in SciPy to discuss a possible deprecation. Are there statistical applications for these parameter finders? I only know that for example `statsmodels` uses the inverse with respect to the noncentrality which is well behaved unlike the ones we are looking at here.  
  
After a quick search on github, it doesn't look like any routines in scipy call `ncfdtridfd` or `ncfdtridfn` (besides where they are defined). Tbh, I'm not familiar with the use cases for _any_ of these inverses. I'm not that familiar with `statsmodels` though.

---

## Comment 10

> Username: steppi  
> Created_at: 2026-02-23 19:59:41 UTC  
> Url: https://github.com/boostorg/math/pull/1368#issuecomment-3946997853  

`ncfdtridfd` can be useful for sample size calculations for ANOVA. `dfn` would be the number of treatments. `p` the desired power `nc` calculated based on the desired effect size. `x` the critical value from the central F distribution for the desired type 1 error rate. Then `dfd` would be the number of replicates to choose per treatment to achieve the desired power to detect the given effect size at the given type 1 error rate. I suspect that in most (all?) practical examples arising from real sample size calculations, there will only be one root.  
  
I'm struggling to think of a use for `ncfdtridfn` though. The effect size of interest is mixed into `dfn` and `nc`, (`nc` is essentially the expected value of the numerator sum of squares, which depends on both the number of treatments and the treatment effect). So `ncfdtridfn` isn't what one would need to do power calculations for how many groups one can hope to investigate given a fixed sample size or things like that. I'd be reluctant to get rid of it in SciPy just because I can't think of an application offhand though.

---
