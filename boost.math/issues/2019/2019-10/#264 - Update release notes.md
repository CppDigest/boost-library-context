# #264 - Update release notes [Closed]

> Username: jzmaddock  
> Created at: 2019-10-20 10:42:10 UTC  
> Updated at: 2019-10-20 19:24:09 UTC  
> Closed at: 2019-10-20 19:24:08 UTC  
> Url: https://github.com/boostorg/math/issues/264  

What do we need in the release notes for 1.72?

---

## Comment 1

> Username: NAThompson  
> Created at: 2019-10-20 12:40:32 UTC  
> Updated at: 2019-10-20 12:47:35 UTC  
> Url: https://github.com/boostorg/math/issues/264#issuecomment-544248658  

- Jacobi polynomials  
- Gegenbauer polynomials  
- Cardinal B-splines as continuous functions  
- Cardinal quintic B-spline interpolation which allows stable evaluation of second derivatives of interpolated data  
- Cardinal trigonometric interpolation  
- Deprecation of `boost/math/tools/univariate_statistics.hpp` and `boost/math/tools/bivariate_statistics.hpp`, in favor of `boost/math/statistics/univariate_statistics.hpp` and `boost/math/statistics/bivariate_statistics.hpp`.  
- One-sample Student's t-test  
- Anderson-Darling test for normality  
- Ljung-Box test for autocorrelation  
- Runs test-a distribution-free test of randomness  
- Empirical cumulative distribution function

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-10-20 12:45:51 UTC  
> Url: https://github.com/boostorg/math/issues/264#issuecomment-544249080  

What is the release schedule? Should we do a merge to master and freeze right now?  
  
I was hoping to get ordinary least squares regression and median absolute deviation linear regression in before the release. . .

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-10-20 14:10:22 UTC  
> Url: https://github.com/boostorg/math/issues/264#issuecomment-544255874  

We have a week before major changes should be frozen, and 2 before a complete freeze: https://www.boost.org/development/index.html

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-10-20 15:07:55 UTC  
> Url: https://github.com/boostorg/math/issues/264#issuecomment-544260895  

Ok, let's merge to develop now to make sure we don't have any problems and we'll see if there's time to get the linear regression in contingent on the result of that. You have anything you still want in?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-10-20 19:24:08 UTC  
> Url: https://github.com/boostorg/math/issues/264#issuecomment-544283463  

I've updated our notes and merged to master.  
  
There may or may not be other merges before 1.72....
