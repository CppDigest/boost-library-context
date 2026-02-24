# #331 Improvements for math_unit_test [Merged]

> Username: NAThompson  
> Created at: 2020-03-28 12:39:58 UTC  
> Updated at: 2020-04-25 12:57:05 UTC  
> Merged at: 2020-04-25 12:56:59 UTC  
> Closed at: 2020-04-25 12:56:59 UTC  
> Url: https://github.com/boostorg/math/pull/331  



---

## Review 1 [Approved]

> Username: pabristow  
> Created_at: 2020-03-29 15:17:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/331#pullrequestreview-383422301  

Should the macro CHECK_CONDITIONED_ERROR be Boostified, for example  
  
BOOST_MATH_CHECK_CONDITIONED_ERROR  
  
and similarly  
  
BOOST_MATH_CHECK_LE  
  
(this one is even more likely to clash with some unsuspecting users usage?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-03-29 15:38:13 UTC  
> Url: https://github.com/boostorg/math/pull/331#issuecomment-605655754  

I'm going to vote for no since I don't want to step on boost.test toes. In addition it makes the macro pretty verbose.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-03-31 00:28:59 UTC  
> Url: https://github.com/boostorg/math/pull/331#issuecomment-606323981  

@jzmaddock : Is this a post-release merge or is it low enough risk to go in now?

---

## Comment 4

> Username: pabristow  
> Created_at: 2020-03-31 09:48:33 UTC  
> Url: https://github.com/boostorg/math/pull/331#issuecomment-606517974  

I don't think that this treads on Boost.Test toes at all - it has BOOST_MATH_ prefix. There is a long standing convention that all macro names used in boost are BOOST_ prefixed, and usually with a library specific like BOOST_MATH_  (Having been bitten by past experience, worse of all MAX and MIN by Microsoft) But I agree it is rather long.  Can we think of a shorter name?   
  
I like the concept a lot., even if we might refine it in the light of experience.  I'm doing some experiments with it.

---
