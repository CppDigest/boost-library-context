# #779 - Unexpected `float` variable in all `expm1_imp` implementations. [Closed]

> Username: WarrenWeckesser  
> Created at: 2022-04-08 00:56:55 UTC  
> Updated at: 2022-04-08 10:21:11 UTC  
> Closed at: 2022-04-08 01:53:32 UTC  
> Url: https://github.com/boostorg/math/issues/779  

This is a question, not a bug report.  Sorry if this is an inappropriate of use a github issue!  
  
I was looking into the implementation of `expm1`, and I noticed that all the versions of `expm1_imp`, regardless of the number of bits of precision, have these lines:  
  
```  
   static const float Y = 0.10281276702880859e1f;  
   [...]  
   T result = x * Y + x * tools::evaluate_polynomial(n, x) / tools::evaluate_polynomial(d, x);  
```  
I was surprised that a `float` constant is used in all cases.  Does the low precision of `Y` not matter, because of how the rational approximation is derived?

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2022-04-08 01:53:32 UTC  
> Url: https://github.com/boostorg/math/issues/779#issuecomment-1092364330  

After looking into this some more, I think I can answer my own question.  `Y` appears to be (more or less) the slope of the linear regression of a sample of `expm1(x)` on [-1/2, 1/2], so the term `x * Y` provides the linear trend of the function on that interval, and the rational approximation was presumably derived with that linear trend removed.  A single precision constant for the slope of the linear trend is probably good enough for that purpose.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-04-08 10:21:11 UTC  
> Url: https://github.com/boostorg/math/issues/779#issuecomment-1092710137  

Short answer:  Y is an arbitrary value, so it is chosen so that it has an exact binary representation in type `float`.  
  
Long answer:  
  
Suppose you want to approximate some function f(x), you begin by finding an elementary function g(x) which is a first approximation, now you reduce the problem to finding an approximation to f(x)/g(x).  In this case `g(x) = x`.  
  
If g(x) is a good enough approximation, then f(x)/g(x) approximates to a constant, so we can write:  
  
f(x)/g(x) = Y + R(x)  
  
Where R(x) is the rational approximation to the residual part.  We can choose whatever value we want for Y, but if we choose so that R(x) is always much less than Y, then the addition step Y + R(x) will effectively erase any small error incurred in computing R(x).  We can't guarantee < 0.5ulp accuracy for every input because we may occasionally get double-rounding, but we should get < 0.5ulp in _nearly_ every case.  
  
Good choices for Y would be the average value of f(x)/g(x) for our domain, or the exact value at an endpoint (so that R(x) is zero at one endpoint).  In fact in this case we could have chosen Y = 1 since that's the limit as x->0.  In any case, as long as we choose an exact binary value and then calculate the coefficients of R to match everything is fine, and only the coefficients of R need storing at full precision.
