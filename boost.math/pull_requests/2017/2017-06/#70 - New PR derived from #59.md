# #70 New PR derived from #59 [Merged]

> Username: jzmaddock  
> Created at: 2017-06-16 17:38:30 UTC  
> Updated at: 2019-11-02 11:23:23 UTC  
> Merged at: 2017-06-17 07:14:15 UTC  
> Closed at: 2017-06-17 07:14:15 UTC  
> Url: https://github.com/boostorg/math/pull/70  

Created this PR so we can get CI testing, also contains many minor fixes.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2017-06-16 18:26:49 UTC  
> Url: https://github.com/boostorg/math/pull/70#issuecomment-309100104  

Just so you don't have to fix this on all my other PRs, what is the benefit of using  ` boost::math::tools::epsilon<Real>()` over `std::numeric_limits<Real>::epsilon()`?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-06-16 18:33:49 UTC  
> Url: https://github.com/boostorg/math/pull/70#issuecomment-309101826  

On 16/06/2017 19:26, Nick wrote:  
>  
> Just so you don't have to fix this on all my other PRs, what is the   
> benefit of using |boost::math::tools::epsilon<Real>()| over   
> |std::numeric_limits<Real>::epsilon()|?  
>  
  
It's designed to work with types that don't specialize numeric_limits -   
for example types with runtime variable precision. Testing with both   
real_concept and std_real_concept should catch any conceptual errors   
like this.  
  
Best, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
