# #445 - Autodiff pow(0,N) always returns undefined derivatives [Closed]

> Username: wgledbetter  
> Created at: 2020-10-27 21:38:31 UTC  
> Updated at: 2020-11-16 16:52:29 UTC  
> Closed at: 2020-11-16 16:52:29 UTC  
> Url: https://github.com/boostorg/math/issues/445  

Line 1415 of `autodiff.hpp` divides by zero when `pow(0, N)` is called. This leads to undefined derivatives for something like x^2.  
  
Might be better to call `pow(x,N-1)`.

---

## Comment 1

> Username: pulver  
> Created at: 2020-10-29 17:45:38 UTC  
> Url: https://github.com/boostorg/math/issues/445#issuecomment-718916073  

The forthcoming implementation/fix will be based on the following limit calculation:  
![image](https://user-images.githubusercontent.com/39707/97604273-ae736200-19e3-11eb-83aa-048b47d7c581.png)  
  
Justification for each of the 4 cases are, resp.  
1. If 0 < c, then there are 2 cases:  
  a. If c is an integer, then x ^ c is continuous and well-defined at x = 0.  
  b. If c is not an integer, then x ^ c is defined only for non-negative x, so only the right-sided limit is relevant.  
2. Though 0^0 is in general indeterminate, x^0 = 1 for all x != 0. Thus the limit of 1 for this case is appropriate.  
3. If c < 0 and c is odd, then the right-sided limit goes to +infinity, and the left-sided limit goes to -infinity, thus NaN is appropriate for this case.  
4. The last case can be split into 2 subcases:  
  a. If c < 0 and c is even, then the right-sided limit and left-sided limit go to +infinity.  
  b. If c < 0 and c is not an integer, then the domain of x is limited to positive values. Thus only the right-sided limit is considered, which is +infinity.  
  
In all cases, c is considered a constant, and limits are taken only with respect to x. In the `autodiff` library, there is a separate overload of `pow(x, c)` in which `c` is also a variable of differentiation, and the limit calculations are likewise different.  
  
Comments are welcome.

---

## Comment 2

> Username: pulver  
> Created at: 2020-11-11 22:49:03 UTC  
> Url: https://github.com/boostorg/math/issues/445#issuecomment-725704437  

Evidently `std::pow()` already handles the above special cases for pow(0,...) so when |x|<epsilon we will just call `std::pow(x,...)` to calculate them: https://en.cppreference.com/w/cpp/numeric/math/pow  
  
We are currently waiting for PR #456 to go through CI build/test: https://travis-ci.org/github/boostorg/math/builds/743053736

---

## Comment 3

> Username: pulver  
> Created at: 2020-11-16 14:58:34 UTC  
> Url: https://github.com/boostorg/math/issues/445#issuecomment-728115539  

@wgledbetter I wasn't able to add you as a reviewer, but if you are able, your comments are welcome on https://github.com/boostorg/math/pull/456
