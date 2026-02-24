# #239 - Reciprocal square roots [Closed]

> Username: NAThompson  
> Created at: 2020-05-22 22:14:23 UTC  
> Updated at: 2020-05-31 02:06:17 UTC  
> Closed at: 2020-05-31 02:06:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239  

Is there a function to compute 1/sqrt(x) in multiprecision?  
  
The Newton’s method for the reciprocal square root does not require division.

---

## Comment 1

> Username: ckormanyos  
> Created at: 2020-05-23 06:24:31 UTC  
> Updated at: 2020-05-23 06:25:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-632994079  

John might correct me, but if I got it right,...  
  
> Is there a function to compute 1/sqrt(x) in multiprecision?  
  
Thank you for this astute observation.  
Nonetheless, i fear the answer is, no.  
  
> The Newton’s method for the reciprocal square root does not require division.  
  
`cpp_dec_float` uses a method called _coupled_ Newton iteration which computes sqrt(x) directly without requiring the inverse operation. Whereas `cpp_bin_float` uses bit shift and bit test on the underlying integral representation.  
  
There is probably some squeezing room all around for optimization.  
  
Kind regards, Chris

---

## Comment 2

> Username: pabristow  
> Created at: 2020-05-23 13:58:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-633059105  

Coupled Newton iteration?  ;-)  (I had to consult the header file to find this! Even dr Google couldn't guess)

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-05-24 17:25:22 UTC  
> Updated at: 2020-05-25 17:32:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-633263538  

Just found out it's already a canned function [in MPFR](https://pdfs.semanticscholar.org/7767/d043abdd223e646a4a999c74b47e22b57902.pdf).  
  
Here's an example use:  
  
```cpp  
using Real = boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<100>>;  
Real res;  
mpfr_rec_sqrt (res.backend().data(), x.backend().data(), GMP_RNDN);  
return res;  
```  
  
The savings are significant: Using `1/sqrt(x)` we have  
  
```  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<100>>>         583 ns          582 ns      1195355  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<200>>>         807 ns          807 ns       861454  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<300>>>        1084 ns         1083 ns       646771  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<400>>>        1384 ns         1382 ns       502235  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<500>>>        1881 ns         1877 ns       440934  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<1000>>>       4285 ns         4278 ns       128090  
````  
  
and using `mpfr_rec_sqrt` we have  
  
```  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<100>>>         441 ns          441 ns      1561433  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<200>>>         606 ns          605 ns      1137047  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<300>>>         800 ns          798 ns       865351  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<400>>>        1022 ns         1020 ns       668175  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<500>>>        1278 ns         1276 ns       538379  
RSqrtBM<boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<1000>>>       3367 ns         3364 ns       210416  
```

---

## Comment 4

> Username: NAThompson  
> Created at: 2020-05-25 17:33:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-633662766  

Is there a way to detect an MPFR type in an `if constexpr`? I have the following useless hack as a workaround:  
  
```  
template<typename Real>  
inline Real rsqrt(Real const & x) {  
    if constexpr (std::is_same_v<Real, float> || std::is_same_v<Real, double> ||  std::is_same_v<Real, long double>) {  
        return 1/sqrt(x);  
    }  
    if constexpr (std::is_same_v<Real, boost::multiprecision::float128>) {  
        return 1/sqrtq(static_cast<__float128>(x.backend().value()));  
    }  
    else if constexpr (sizeof(Real) > 16) {  
        Real res;  
        mpfr_rec_sqrt (res.backend().data(), x.backend().data(), GMP_RNDN);  
        return res;  
    }  
}  
```

---

## Comment 5

> Username: NAThompson  
> Created at: 2020-05-25 21:02:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-633715341  

Fixed in #240

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-05-26 09:16:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-633909153  

At the expense of a little bit of verbosity, I would use good old fashioned overloading for this:  
  
```  
template <unsigned Digits10, boost::multiprecision::mpfr_allocation_type AllocateType, boost::multiprecision::expression_template_option ExpressionTemplates>  
boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<Digits10, AllocateType>, ExpressionTemplates> rsqrt(const boost::multiprecision::number<boost::multiprecision::mpfr_float_backend<Digits10, AllocateType>, ExpressionTemplates>& arg);  
  
boost::multiprecision::float128 rsqrt(boost::multiprecision::float128 arg);  
  
float rsqrt(float);  
double rsqrt(double)  
long double rsqrt(long double);  
// etc  
```

---

## Comment 7

> Username: NAThompson  
> Created at: 2020-05-31 02:06:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/239#issuecomment-636409675  

Fixed in #240
