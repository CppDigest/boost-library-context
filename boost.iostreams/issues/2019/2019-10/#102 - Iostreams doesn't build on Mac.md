# #102 - Iostreams doesn't build on Mac [Closed]

> Username: pdimov  
> Created at: 2019-10-26 11:52:39 UTC  
> Updated at: 2019-11-19 05:07:01 UTC  
> Closed at: 2019-11-19 05:07:01 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102  

The Mac build on Travis fails with a link error caused by PR #95, but the job is marked as allowed failure due to an unrelated issue #91.  
  
Whether liblzma contains the necessary MT functions should be checked by the Jamfile using `check-target-builds`. (Add a source file `has_lzma_cputhreads.cpp`, then add `[ check-target-builds has_lzma_cputhreads : : define=BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED=1 ]` to `lzma-requirements`.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-26 12:45:44 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546599644  

I'm working on a pull request.

---

## Comment 2

> Username: jbonyun  
> Created at: 2019-10-26 16:11:30 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546616306  

Well that looks bad. We only escaped this earlier because an unrelated "allowed failure" let us ignore it? Curse our over-reliance on automated tools!  
  
My bjam skillz are insufficient to handle this well, so I'm glad you have a solution in mind already. I really appreciate your effort in cleaning up my mess.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-10-26 16:26:18 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546617702  

The solution I have in mind is on the `pr/check-lzma-cputhreads` branch; maybe you could give it a go while we wait for Travis to get around to it, because I don't have a configuration with a recent liblzma on which to test (my Xenial VM only has libzma 5.1.1.)  
  
With liblzma >= 5.2, building Iostreams should say something like `has_lzma_cputhreads builds: yes` and enable MT support.

---

## Comment 4

> Username: pdimov  
> Created at: 2019-10-26 23:44:12 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546648861  

#103. Confirmed by @glenfe.

---

## Comment 5

> Username: pdimov  
> Created at: 2019-10-26 23:46:38 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546648954  

It might still be a good idea for someone with Mac access to investigate and fix the test failures, or if not that, at least disable the failing tests on Mac so that the job is no longer marked as allowed to fail.

---

## Comment 6

> Username: pdimov  
> Created at: 2019-10-26 23:49:22 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-546649086  

Looks like clang-cl on Windows has the same failure: https://ci.appveyor.com/project/eldiener/iostreams/builds/28399267/job/d25uvfid3axheonf#L1201

---

## Comment 7

> Username: mclow  
> Created at: 2019-11-18 20:04:08 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-555185987  

I'm seeing this on Mac OS X 10.14 with the 1.72.0beta RC1.  `LZMA_VERSION` 50020042

---

## Comment 8

> Username: mclow  
> Created at: 2019-11-18 20:05:17 UTC  
> Url: https://github.com/boostorg/iostreams/issues/102#issuecomment-555186411  

My suggested change was:  
```diff  
#ifndef BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED  
-    #if LZMA_VERSION < 50020002  
+   #if LZMA_VERSION <= 50020042  
        #define BOOST_IOSTREAMS_LZMA_NO_MULTITHREADED  
    #endif  
#endif  
```  
  
But that's just a quick fix.
