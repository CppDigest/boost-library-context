# #24 Use BOOST_THREAD_RV_REF for move constructors [Merged]

> Username: MarcelRaad  
> Created at: 2014-08-26 08:18:47 UTC  
> Updated at: 2014-08-27 17:59:18 UTC  
> Merged at: 2014-08-26 16:44:38 UTC  
> Closed at: 2014-08-26 16:44:38 UTC  
> Url: https://github.com/boostorg/thread/pull/24  

If BOOST_THREAD_FWD_REF is used and move emulation is active, the copy constructor is re-declared. Tested with clang 3.4.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-08-26 21:45:59 UTC  
> Url: https://github.com/boostorg/thread/pull/24#issuecomment-53497636  

Unfortunately, there are a lot of errors after applying the patch.  
Have you run the regression test on your platform/compiler? if yes with which one?  
I will rollback the merge and see what can be done.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2014-08-27 10:55:05 UTC  
> Url: https://github.com/boostorg/thread/pull/24#issuecomment-53553936  

Sorry for that! I didn't run the full regression test suite as I thought that building Boost from develop was broken (I only found out yesterday that only building all of Boost including Endian is broken). I ran the tests in libs/thread/test/functional/invoker and compiled our own code. Our own code was broken on clang with libstdc++ as it complained about the copy constructor defined twice and this patch repaired it. The tests in test/functional/invoker that worked before the patch continued to work after it.

---

## Comment 3

> Username: viboes  
> Created_at: 2014-08-27 11:39:28 UTC  
> Url: https://github.com/boostorg/thread/pull/24#issuecomment-53557636  

Could you try the develop branch after this commit?  
  
https://github.com/boostorg/thread/commit/957253401f420c5a8c8f910cd76a41dfe927b8bc

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2014-08-27 17:59:18 UTC  
> Url: https://github.com/boostorg/thread/pull/24#issuecomment-53614137  

BOOST_THREAD_COPYABLE_AND_MOVABLE compiles with all our setups (with both BOOST_THREAD_FWD_REF and BOOST_THREAD_RV_REF). Thanks a lot!

---
