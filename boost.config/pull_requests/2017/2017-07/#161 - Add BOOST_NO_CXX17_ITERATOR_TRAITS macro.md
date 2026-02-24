# #161 Add BOOST_NO_CXX17_ITERATOR_TRAITS macro. [Merged]

> Username: Lastique  
> Created at: 2017-07-09 16:08:27 UTC  
> Updated at: 2017-08-24 12:08:05 UTC  
> Merged at: 2017-07-21 18:40:18 UTC  
> Closed at: 2017-07-21 18:40:18 UTC  
> Url: https://github.com/boostorg/config/pull/161  

The macro indicates that the standard library does not implement SFINAE-friendly  
`std::iterator_traits` (LWG issue 2408 http://cplusplus.github.io/LWG/lwg-defects.html#2408, [iterator.traits]/2).

---

## Review 1 [Approved]

> Username: glenfe  
> Created_at: 2017-07-09 21:16:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/config/pull/161#pullrequestreview-48787242  

Looks good.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-07-21 17:41:12 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-317065874  

My only query - is it really correct that libc++ doesn't support this - they're normally right at the forefront of these new features?

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-21 17:52:50 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-317068920  

The feature is not implemented according to https://libcxx.llvm.org/cxx1z_status.html (search for 2408). Travis tests also passed, suggesting that this is truly the case.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-07-21 18:40:12 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-317080665  

On 21/07/2017 18:52, Andrey Semashev wrote:  
>  
> The feature is not implemented according to   
> https://libcxx.llvm.org/cxx1z_status.html (search for 2408). Travis   
> tests also passed, suggesting that this is truly the case.  
>  
  
If I'm reading that correctly, it's implemented in latest SVN but not   
released as such?  
  
BTW the Travis tests don't currently catch macros that are unnecessarily   
defined, but whatever, will merge shortly.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-07-21 19:30:53 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-317092296  

> If I'm reading that correctly, it's implemented in latest SVN but not released as such?  
  
My understanding of the second table is that it lists issues that were resolved by the Committee but not yet implemented in libc++. Maybe I got confused. In any case, libc++ 3.9.1 that I have locally doesn't implement it and we generally don't support unreleased versions of compilers.  
  
I'll watch the test matrix if test failures appear. Thanks.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-08-24 10:15:23 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-324594626  

Could you merge this PR and https://github.com/boostorg/config/pull/162 to master, please?

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2017-08-24 12:08:05 UTC  
> Url: https://github.com/boostorg/config/pull/161#issuecomment-324616718  

On 24/08/2017 11:15, Andrey Semashev wrote:  
>  
> Could you merge this PR and #162   
> <https://github.com/boostorg/config/pull/162> to master, please?  
>  
  
See https://github.com/boostorg/config/pull/183, just waiting for CI to   
cycle.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
