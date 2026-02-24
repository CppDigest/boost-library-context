# #233 Gegenbauer polynomials [Merged]

> Username: NAThompson  
> Created at: 2019-08-04 13:27:20 UTC  
> Updated at: 2019-08-27 11:48:06 UTC  
> Merged at: 2019-08-27 11:47:58 UTC  
> Closed at: 2019-08-27 11:47:58 UTC  
> Url: https://github.com/boostorg/math/pull/233  

I think this will be easier than the Jacobi polynomials; in any case, my use case only requires that I have some continuous parameter that I can optimize over, and the Gegenbauer polynomials give me one.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-04 16:49:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/233#pullrequestreview-270505904  

📁 include/boost/math/special_functions/gegenbauer.hpp

```diff
   4 |+ //  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)
   5 |+ 
   6 |+ #ifndef BOOST_MATH_SPECIAL_CARDINAL_B_SPLINE_HPP
```

> Username: jzmaddock  
> Created_at: 2019-08-04 16:49:26 UTC  
> Updated_at: 2019-08-23 13:23:43 UTC  
> Url: https://github.com/boostorg/math/pull/233#discussion_r310395039  

The include guard name needs changing - cut and paste error?

> Username: NAThompson  
> Created_at: 2019-08-04 17:09:02 UTC  
> Updated_at: 2019-08-23 13:23:43 UTC  
> Url: https://github.com/boostorg/math/pull/233#discussion_r310395803  

Yup, just got that one.


---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2019-08-04 16:51:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/233#pullrequestreview-270505976  

📁 include/boost/math/special_functions/gegenbauer.hpp

```diff
  33 |+     Real2 factor = 1;
  34 |+     boost::uintmax_t max_iter = n + 1;
  35 |+     Real2 Calphax = boost::math::tools::function_ratio_from_backwards_recurrence(Recurrence, factor, max_iter);
```

> Username: jzmaddock  
> Created_at: 2019-08-04 16:51:55 UTC  
> Updated_at: 2019-08-23 13:23:43 UTC  
> Url: https://github.com/boostorg/math/pull/233#discussion_r310395134  

Do you really want the ratio here?  Surely you want to apply recurrences up to n - or else recurse down to n = 0 and normalise (Millers Algorithm)?

> Username: NAThompson  
> Created_at: 2019-08-04 17:09:34 UTC  
> Updated_at: 2019-08-23 13:23:43 UTC  
> Url: https://github.com/boostorg/math/pull/233#discussion_r310395819  

I hadn't yet worked out the ratio; actually I just PR'd this to move it from one computer to another.


---

## Comment 3

> Username: NAThompson  
> Created_at: 2019-08-04 17:17:10 UTC  
> Updated_at: 2019-08-04 17:36:38 UTC  
> Url: https://github.com/boostorg/math/pull/233#issuecomment-518020588  

If I've still got John online:  
  
- I have recursed upwards, which is exactly what the GSL does. But OMG, something very weird is happening. Check out the ULP plot:  
  
![Gegenbaurer6ULP](https://user-images.githubusercontent.com/5459618/62426988-0aa0e680-b6ba-11e9-9c62-d285881ecd79.png)  
  
There appears to be 3 distinct regions where the upwards recurrence is unstable. I have no explanation. In addition, the regions have very small measure; almost everywhere else it is perfectly stable.  
  
Here's the code (you'll need to clone [quicksvg](https://github.com/NAThompson/quick_svg):  
  
```cpp  
#include <iostream>  
#include <utility>  
#include <boost/math/special_functions/gegenbauer.hpp>  
#include <quicksvg/ulp_plot.hpp>  
  
using boost::math::gegenbauer;  
int main() {  
  
    int samples = 10000;  
    for (unsigned n = 3; n < 10; ++n)  
    {  
        double a = 0;  
        double b = 1;  
        boost::multiprecision::cpp_bin_float_50 lambda_a = 1;  
        double lambda_d = 1;  
  
        std::string title = "Double precision ULP accuracy of the " + std::to_string(n) + "th Gegenbauer polynomial";  
        std::string filename = "doc/graphs/gegenbauer_ulp_" + std::to_string(n) + ".svg";  
        auto geg_double = [&](double x) { return gegenbauer<double>(n, lambda_d, x); };  
        auto geg_accurate = [&](boost::multiprecision::cpp_bin_float_50 x) { return gegenbauer<boost::multiprecision::cpp_bin_float_50>(n, lambda_a, x); };  
        quicksvg::ulp_plot(geg_double, geg_accurate, a, b, title, filename, samples);  
    }  
  
    return 0;  
}  
```  
  
For n = 3 and lambda = 1, the instability occurs precisely at 1/sqrt(2).  
  
So it looks like maybe recursing down and normalizing is the way to go? But thus far I haven't worked out what the normalization should be.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-08-04 17:47:29 UTC  
> Url: https://github.com/boostorg/math/pull/233#issuecomment-518022571  

That's weird for sure, but the errors are still relatively small?  Is this near the zeros of the polynomial maybe?  I doubt the recursing down would help if so - normalisation would be by comparison to the zeroth order polynomial which is known.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-08-04 17:52:47 UTC  
> Url: https://github.com/boostorg/math/pull/233#issuecomment-518022934  

Actually looking at the recurrence relations It's not at all obvious to me which direction is the stable one.  My suspicion might be that both directions are meta-stable to some extent.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2019-08-04 17:55:37 UTC  
> Updated_at: 2019-08-04 18:21:04 UTC  
> Url: https://github.com/boostorg/math/pull/233#issuecomment-518023125  

Uh, whoops! Looks like you are precisely correct; these are the roots.  
  
I think even though these are roots, there's a way to do a bit better than this via some a posteriori detection of |Cn(x)| < eps?

---

## Comment 7

> Username: NAThompson  
> Created_at: 2019-08-05 00:02:46 UTC  
> Updated_at: 2019-08-05 00:12:57 UTC  
> Url: https://github.com/boostorg/math/pull/233#issuecomment-518047231  

I just did a literature review on this problem: Basically, there are sophisticated mechanisms to sum a series of Gegenbauer polynomials (going beyond the standard Clenshaw recurrence) but it doesn't appear that there is much study of how to evaluate the function near the roots to high accuracy; moreover this problem is ill-conditioned so I'm not sure I should agonize over this too much. The fact that it appears in the quadratic means that there's very little scope for improvement.  
  
In any case, I'm removed the draft label; presumably the only thing to try is continued fractions or Miller's algorithm.

---
