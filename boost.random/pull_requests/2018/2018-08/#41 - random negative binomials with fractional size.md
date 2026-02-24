# #41 random negative binomials with fractional size [Closed]

> Username: madeleineth  
> Created at: 2018-08-09 00:51:16 UTC  
> Updated at: 2022-03-22 21:05:13 UTC  
> Closed at: 2022-03-22 21:05:13 UTC  
> Url: https://github.com/boostorg/random/pull/41  

Support negative binomial random generation with a fractional size parameter. Since it is cast to `RealType` anyway when it is used, I believe this is backward compatible with nearly all use cases.  
  
For more information on why you would want a fractional size parameter, see https://www.johndcook.com/blog/2009/09/22/negative-binomial-distribution/; `boost::math::negative_binomial` already does.  
  
(This is my first pull request to Boost; please let me know if you would like things handled differently. Neither I nor my employer, Tableau, claim copyright on this patch.)

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-08-09 01:06:33 UTC  
> Url: https://github.com/boostorg/random/pull/41#issuecomment-411604162  

AMDG  
  
On 08/08/2018 06:51 PM, Madeleine Thompson wrote:  
> Support negative binomial random generation with a fractional size parameter. Since it is cast to `RealType` anyway when it is used, I believe this is backward compatible with nearly all use cases.  
>   
  
I'm sure I've seen this before.  Ah, here's  
the discussion:  
https://svn.boost.org/trac10/ticket/10110  
  
In Christ,  
Steven Watanabe

---
