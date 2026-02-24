# #380 - Failure of tanh_sinh when used with mpfr_float [Open]

> Username: duhadler  
> Created at: 2020-06-28 08:42:19 UTC  
> Updated at: 2020-07-04 17:10:54 UTC  
> Url: https://github.com/boostorg/math/issues/380  

I have recently started to use the quadrature routines of Boost Math with the ```mpfr_float``` data type of Boost Multiprecision.   
  
To my amazement, the following routines work out of the box:  
  
```  
trapezoidal  
gauss  
exp_sinh  
sinh_sinh  
ooura_fourier_cos  
```  
  
However, ```tanh_sinh``` fails during initialization with the following error message:  
  
```  
Message from thrown exception was :  
Error in function boost::multiprecision::itrunc<class boost::multiprecision::number<struct boost::multiprecision::backends::mpfr_float_backend<0, 1>, 1>>(class boost::multiprecision::number<struct boost::multiprecision::backends::mpfr_float_backend<0, 1>, 1>) :  
Value inf can not be represented in the target integer type.  
```  
  
This can be fixed by making the following change in line 164 of the file  
  
\boost\math\quadrature\detail\tanh_sinh_detail.hpp  
  
```  
Real l = log(sqrt(2 - x) / sqrt(x));     //Original:  Real l = log(sqrt((2 - x) / x));  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-06-28 13:36:08 UTC  
> Updated at: 2020-06-28 16:30:26 UTC  
> Url: https://github.com/boostorg/math/issues/380#issuecomment-650759754  

@duhadler : Thanks! I was just thinking last night that I needed to add some MPFR quadrature tests; looks like this will be a good opportunity to get that done.  
  
I have validated your fix; now I just need to produce an ulps plot to make sure that (say) (log(2-x) - log(x))/2 isn't a better option.

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-06-29 14:58:12 UTC  
> Url: https://github.com/boostorg/math/issues/380#issuecomment-651176296  

For future reference, I found that I had to explicitly specify the return type with MPFR:  
  
```  
    auto f = [](const Real& x)->Real  
    {  
       return 5*x + 7;  
    };  
```  
  
or else I get compile errors on `g++-10`. Not quite sure why this is the case. . .

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-07-04 17:10:54 UTC  
> Url: https://github.com/boostorg/math/issues/380#issuecomment-653790063  

@duhadler : The issue for `tanh_sinh` is now fixed. I'll leave this issue open since there is a broader issue of adding tests for MPFR types that needs to be addressed on a longer timescale.
