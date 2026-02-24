# #183 - Problems about the valid domain of input k in ellint_1, ellint_2, ellint_3 [Closed]

> Username: adamadanandy  
> Created at: 2019-02-16 09:26:39 UTC  
> Updated at: 2019-05-20 12:35:25 UTC  
> Closed at: 2019-05-20 12:35:25 UTC  
> Url: https://github.com/boostorg/math/issues/183  

Directory: _boost/math/special_functions/_  
Files: _ellint_1.hpp_, _ellint_2.hpp_, _ellint_3.hpp_  
  
For the 1st incomplete elliptic integral, `ellint_f_imp(T phi, T k, Policy& pol)` in file _ellint_1.hpp_, the function requires |k|<=1, which is not necessary. The only constraint is that the Carlson elliptic integral R_f, which is called in line 118, requires `(c-k*k)>=0`, where `c=1/sinp` is larger than 1. So the |k|<=1 constraint is too strong in this case. However, in line 80 and line 125, the complete elliptic integral `ellint_k_imp` is called. The complete elliptic integral requires |k|<1, and the result goes to infinity at |k|==1, which implies the |k|<=1 constraint in `ellint_f_imp` is too weak.  
  
The appropriate domain region of k is given by NAG Library (where $m=k^2$):  
[https://www.nag.com/numeric/fl/nagdoc_fl22/xhtml/s/s21bef.xml](url)  
$k^2\sin^2\phi\leq 1$  
  
The same problem exists in 2nd and 3rd incomplete elliptic integral as well.  
  
I know the reason of this constraint is C++17's design, but it really causes some problem in numerical computation.  
  
In C++17, function std::ellint_1 has error handling:  
[https://en.cppreference.com/w/cpp/numeric/special_math/ellint_1](url)  
> Errors may be reported as specified in math_errhandling  
>   
> If the argument is NaN, NaN is returned and domain error is not reported  
> If |k|>1, a domain error may occur

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-02-16 19:48:36 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-464377672  

Thanks for the report: I suspect there are a few functions that can usefully have their domain expanded beyond what the original TR proposed.  Once I have some time I'll look into this.  Or you can speed things up by submitting a PR ;)

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-02-18 03:56:00 UTC  
> Updated at: 2019-02-18 03:56:10 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-464574210  

Looks like there's some work on this in Arb: https://arxiv.org/pdf/1806.06725.pdf  
  
But unfortunately this isn't something I'm good at so I'll be of no help here!

---

## Comment 3

> Username: adamadanandy  
> Created at: 2019-02-18 06:38:21 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-464604549  

> Looks like there's some work on this in Arb: https://arxiv.org/pdf/1806.06725.pdf  
>   
> But unfortunately this isn't something I'm good at so I'll be of no help here!  
  
Thanks for your help, I have read the Chapter 1.6 in your recommended article. It actually is the implementation in boost. The equation (1.30) is written in files ellint_1.hpp, ellint_2.hpp and ellint_3.hpp.  
However, in this article, it doesn't provide the valid domain for "n" and "m". The only constraint is provided in the paragraph after function (1.31), which indicates the branch cut (-\infinity,0) is removed in the complex plane, so for real x,y,z of Carlson elliptic integral, the valid domain of "n" and "m" is still determined by (x,y,z)>0, as mentioned at issue open.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2019-02-18 09:03:37 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-464645661  

I'm sure the domain can be extended, it's just a question of finding the time to work through the details, and make some decent tests.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-05-19 07:59:12 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-493735661  

The referenced PR updates the supported range of ellint_1/2/3/d but not the heuman lambda or jacobi zeta.  The implementation of the latter seems to permit a similarly extended range, while the definition does not, so I'm conflicted on that one.  
Note that some of the internals of ellint_3 did assume that |k| < 1, so if you have a locally patched copy that allows k > 1 it might not always work as expected.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-20 12:35:25 UTC  
> Url: https://github.com/boostorg/math/issues/183#issuecomment-493968231  

Fixes merged to develop.
