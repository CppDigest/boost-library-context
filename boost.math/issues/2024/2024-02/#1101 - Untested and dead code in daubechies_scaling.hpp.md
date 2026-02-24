# #1101 - Untested and dead code in daubechies_scaling.hpp [Open]

> Username: jzmaddock  
> Created at: 2024-02-22 17:44:14 UTC  
> Updated at: 2024-02-23 00:29:15 UTC  
> Url: https://github.com/boostorg/math/issues/1101  

Probably one for @NAThompson.  
  
In daubechies_scaling.hpp  
  
We have one line uncovered here: https://github.com/boostorg/math/blob/2af21de3b7f9935c2818eab7be6eaf2211e4672c/include/boost/math/special_functions/daubechies_scaling.hpp#L367  which appears to be because the constructor is never called in our tests with anything except the default value for `grid_refinements`.  We should have at least one absolute error (-2) value, plus one other positive value.  But I'm not sure which tests and invariants hold in these cases?  
  
Also the types `detail::matched_holder` and `detail::linear_interpolation` appear not to be used by anything, nor mentioned in the docs?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-02-22 17:46:33 UTC  
> Url: https://github.com/boostorg/math/issues/1101#issuecomment-1959954500  

Sorry, one other, there are no tests for double_prime() other than the boundary cases.  Again I'm not sure how to validate the result?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-02-22 18:34:57 UTC  
> Url: https://github.com/boostorg/math/issues/1101#issuecomment-1960033245  

And one more, daubechies_wavelet.hpp is also untested for anything except default argument to `daubechies_wavelet`.  The code for p==1 here: https://github.com/boostorg/math/blob/2af21de3b7f9935c2818eab7be6eaf2211e4672c/include/boost/math/special_functions/daubechies_wavelet.hpp#L212 is also untested.

---

## Comment 3

> Username: NAThompson  
> Created at: 2024-02-23 00:29:13 UTC  
> Url: https://github.com/boostorg/math/issues/1101#issuecomment-1960566139  

@jzmaddock : Yup, you're right. Will try to get to this next week.
