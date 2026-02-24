# #13 Fix to Ticket #7769 [Merged]

> Username: brunocodutra  
> Created at: 2015-02-08 22:17:07 UTC  
> Updated at: 2015-03-19 17:26:39 UTC  
> Merged at: 2015-03-19 17:26:39 UTC  
> Closed at: 2015-03-19 17:26:39 UTC  
> Url: https://github.com/boostorg/mpl/pull/13  

https://svn.boost.org/trac/boost/ticket/7769

---

## Comment 1

> Username: mclow  
> Created_at: 2015-02-09 14:41:26 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-73518958  

This looks fine to me, but it needs a test.  
Yes, I read the bug report [discussion](https://svn.boost.org/trac/boost/ticket/7769) :-)  
The test for this might go in test/aux_/preprocessor; look at the other tests there.

---

## Comment 2

> Username: brunocodutra  
> Created_at: 2015-02-10 11:00:08 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-73681383  

Sure, I'll look into this as soon as I can spare some time.

---

## Comment 3

> Username: brunocodutra  
> Created_at: 2015-02-21 21:36:54 UTC  
> Updated_at: 2015-02-21 21:37:15 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75394210  

So Marshall, are you suggesting I write a test to just assert BOOST_MPL_PP_RANGE is working as expected? Since it depends solely on BOOST_PP, shouldn't we assume it is already tested?  
  
I believe it would be much profitable to test a real use case of increased arity, but I fear breaking something for rare setups on which a maximum arity of 5 should never be extrapolated. I mean, as I understand it, MPL is guaranteed to compile across the various platforms only as long as its limits are not increased, isn't it? If otherwise MPL should indeed support an increase of its limits on all platforms, than I believe a better approach would be to repeat tests for apply, lambda, quote, or anything similar, but with the maximum arity increased by some constant.  
  
What you guys think?

---

## Comment 4

> Username: eldiener  
> Created_at: 2015-02-21 22:26:29 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75396501  

Look at the documentation for BOOST_MPL_LIMIT_METAFUNCTION_ARITY. The way to test that your change is working correctly is to write a test changing the metafunction arity to some number above 9 with a metafunction of that arity and an 'apply' which works as one successful test, and write another test with a metafunction above your larger arity and an 'apply' which gives an error as a failed test. Those tests will show whether or night your change is working properly in MPL. Of course you are free to use other processing on your metafunction than just   
apply' if you want. Look also at the comment about defining BOOST_MPL_CFG_NO_PREPROCESSED_HEADERS for your two individual tests.  
  
As far as someone else's setup the default maximum arity is still 5 if BOOST_MPL_LIMIT_METAFUNCTION_ARITY is not defined. You are not breaking that. If you are concerned about other Boost libraries using BOOST_MPL_LIMIT_METAFUNCTION_ARITY and possibly being broken in light of your own change, then run the tests already in place for that library with your change as part of MPL. Eventually when your change goes to the 'develop' branch for testing it would be good if you can keep your eye on any suspicious results you might see.

---

## Comment 5

> Username: brunocodutra  
> Created_at: 2015-02-21 22:47:51 UTC  
> Updated_at: 2015-02-21 22:48:40 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75397449  

Thank you for your comments Edward, but I think you missed the point regarding my previous comment.  
  
I do understand the rationale behind BOOST_MPL_LIMIT_METAFUNCTION_ARITY and the accompanying BOOST_MPL_CFG_NO_PREPROCESSED_HEADERS. I am also confident I can think of a way to correctly test the fix and I'll make sure to run existing tests with a maximum arity set to, say 10, just so we are more comfortable with it.  
  
My only concern is that, once a test which effectively increases maximum arity is added, then volunteers who run the test suite on setups which do not support an arity higher than 5 will start to report failures. Please note that my concern does not apply for the end user, just for the testing system itself. The point here is that I do not know for certain whether such setups exist, where a maximum arity higher than 5 breaks stuff, the only hint I have is that this constant of 5 must have been set for a reason.   
  
Well, maybe I'm just being overly cautious on this one. I think I'll just go ahead and write this test and watch for anything suspicious on the test reports.

---

## Comment 6

> Username: eldiener  
> Created_at: 2015-02-22 00:32:40 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75406124  

The default arity will not be higher than 5 unless someone sets it to be higher by using the BOOST_MPL_LIMIT_METAFUNCTION_ARITY macro to do so. I am assuming that your fix does not change the default arity in MPL. If it does, except for your specific tests which exists only as part of MPL, it is a mistake

---

## Comment 7

> Username: brunocodutra  
> Created_at: 2015-02-22 00:38:09 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75407651  

Correct.  
  
The only possible inconvenience I foresee is the failure of these specific tests on very peculiar setups. For now I assume no such weird platform exists, where arity may never be increased past 5, but if it does, guards could be added in order to avoid these tests to be run on these specific setups.

---

## Comment 8

> Username: brunocodutra  
> Created_at: 2015-02-22 18:53:40 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-75450847  

I added some test cases as you guys advised.   
Could you kindly review the patch?

---

## Comment 9

> Username: mclow  
> Created_at: 2015-03-18 16:38:24 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-83050451  

This looks OK to me. Anyone see any problems?

---

## Comment 10

> Username: swatanabe  
> Created_at: 2015-03-19 15:41:39 UTC  
> Url: https://github.com/boostorg/mpl/pull/13#issuecomment-83638818  

AMDG  
  
On 03/18/2015 10:38 AM, Marshall Clow wrote:  
  
> This looks OK to me. Anyone see any problems?  
  
Nope.  It looks fine to me.  
  
In Christ,  
Steven Watanabe

---
