# #372 - Bug in ibeta_power_terms [Closed]

> Username: evanmiller  
> Created at: 2020-06-17 21:25:14 UTC  
> Updated at: 2020-07-31 17:55:48 UTC  
> Closed at: 2020-07-31 17:55:48 UTC  
> Url: https://github.com/boostorg/math/issues/372  

Thank you for an excellent library. I don't have a test case for this, but the following line of code is not correct:  
  
https://github.com/boostorg/math/blob/3cc1ebef5e5ece525770692907e7d10df84ec5a4/include/boost/math/special_functions/beta.hpp#L517  
  
I.e. it both adds and subtracts `lgamma(c)`. I believe the line should instead read:  
  
```  
 log_result += boost::math::lgamma(c, pol) - boost::math::lgamma(a) - boost::math::lgamma(b, pol);  
```  
  
It is somewhat concerning that the commit that added this code https://github.com/boostorg/math/commit/238436ee0781593e6c38fe240b390a251bad72e3 also loosened the test tolerances in several places. However, I have not run the tests with the proposed modification to see if the fix brings the code back in line with the old tolerances.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-18 01:00:14 UTC  
> Updated at: 2020-06-18 01:05:42 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-645705698  

Thanks for the heads up! That definitely looks like a bug.  
  
What (rough) values of the arguments did you see this? Looks like we have to get `isnormal(x)` on line 515 to be false to hit it.  
  
I just made an ULPs plot and couldn't hit that condition (although of course this will be tautological if both the `long double` and `float` implementation are wrong.)  
  
```cpp  
#include <iostream>  
#include <string>  
#include <boost/math/tools/ulps_plot.hpp>  
#include <boost/math/special_functions/beta.hpp>  
  
int main() {  
    auto f = [](long double x) {  
        return boost::math::beta(x, 2);  
    };  
  
    auto g = [](float x) {  
        return boost::math::beta(x, float(2));  
    };  
  
    using boost::math::tools::ulps_plot;  
  
    std::string filename = "beta_bug.svg";  
    int samples = 10000;  
    int width = 700;  
    long double clip = 2.5;  
    auto plot = ulps_plot<decltype(f), long double, float>(f, float(2), float(10), samples);  
  
    plot.clip(clip).width(width);  
    plot.add_fn(g);  
    plot.write(filename);  
}  
```  
  
<img width="692" alt="Screen Shot 2020-06-17 at 8 59 04 PM" src="https://user-images.githubusercontent.com/5459618/84965631-63fce300-b0dd-11ea-960c-8cb62ad6ea27.png">  
  
Here's the version without `float` promotion:  
  
<img width="702" alt="Screen Shot 2020-06-17 at 9 01 08 PM" src="https://user-images.githubusercontent.com/5459618/84965744-b76f3100-b0dd-11ea-943f-e3f5c2905874.png">

---

## Comment 2

> Username: evanmiller  
> Created at: 2020-06-18 01:37:22 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-645716469  

Thank you for the prompt reply. I just came across the line in question while trying to understand the code - it's possible that the pathway is not hit at all.

---

## Comment 3

> Username: evanmiller  
> Created at: 2020-06-18 01:45:51 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-645718982  

Note that the triggering condition is `!isnormal(result)` rather than `!isnormal(x)`.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-06-18 12:14:37 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-645979478  

Good spot!  It's clearly my mistake - the final `- boost::math::lgamma(c, pol)` should be `- boost::math::lgamma(b, pol)`.  Which is to say we are dividing (by logs) by `beta(a, b)`.  
  
@NAThompson : this won't show up with type double or float - this code only kicks in for types with no available Lanczos approximation (ie multiprecision types).  
  
I'm testing now to see if we have coverage of this, or if it's a bit of defensive programming against spurious overflow...

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-07-24 07:28:49 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-663385630  

PR fix pushed, thanks for the nudge!

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-07-28 10:51:49 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-664971384  

I think this should now be addressed in develop.

---

## Comment 7

> Username: evanmiller  
> Created at: 2020-07-28 11:25:58 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-664985088  

@jzmaddock Nope:  
  
https://github.com/boostorg/math/blob/fa23c83bb1b46a2a21a98f667f01db0f18b6e486/include/boost/math/special_functions/beta.hpp#L517  
  
It's fixed in the `issue372` branch but this was never merged. While we're here, the line needs a second correction – passing the policy argument in the second call to `lgamma`.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-07-28 16:11:01 UTC  
> Url: https://github.com/boostorg/math/issues/372#issuecomment-665132371  

My bad, as the PR wasn't on my radar I thought I'd merged it already, but apparently I'd never made it a PR in the first place :(  Done now.
