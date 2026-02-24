# #25 - Add exact calculation of higher order moments [Open]

> Username: NAThompson  
> Created at: 2019-01-18 17:11:45 UTC  
> Updated at: 2019-01-18 17:14:17 UTC  
> Url: https://github.com/boostorg/accumulators/issues/25  

I noticed [here](https://github.com/boostorg/accumulators/blob/b133840d2b3aa8c4bf07a60c00c5f4ff1396b45e/include/boost/accumulators/statistics/variance.hpp), line 58, that the variance is not calculated exactly, but rather calculated using an approximation.  
  
I ran a `git blame` on this file as well as the higher order moments and found that at the time it was written (2008), there was no known exact, numerically stable, single pass, online algorithm which can compute higher order moments. This changed in 2009 with the publication of the following paper:  
  
* Philippe P. Pébay: ["Formulas for Robust, One-Pass Parallel Computation of Covariances and Arbitrary-Order Statistical Moments.] Technical Report SAND2008-6212, Sandia National Laboratories, September 2008.  
  
Updating the variance, covariance, skewness, kurtosis, and all higher order moments to use these formulas is no small task, but since the current approximation seems uncontrolled, at some point it seems like it is reasonable to add them to the accumulators library.
