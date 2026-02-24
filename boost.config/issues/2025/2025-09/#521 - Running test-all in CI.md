# #521 - Running test/all in CI [Open]

> Username: anarthal  
> Created at: 2025-09-18 11:10:27 UTC  
> Updated at: 2025-09-18 16:00:04 UTC  
> Url: https://github.com/boostorg/config/issues/521  

This is both a question and an offer for help. I'm trying to introduce some modifications in Boost.Config for C++20 modules in gcc to build. I've seen a plethora of tests under `test/all` which cover the macros I'm touching, but these seem not to be run by the CIs. In fact, some of them seem to error when running them under gcc-13 in my Ubuntu 24 machine.  
  
Would you accept PRs fixing the current build failures, adding these tests to CIs, and the like?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-09-18 16:00:04 UTC  
> Url: https://github.com/boostorg/config/issues/521#issuecomment-3308293989  

Yes by all means some help would be much appreciated.  CI appears to have atrophied again and needs some help to get green again.  
  
With regard to the tests under test/all these are intentionally not run under CI: they are automatically generated, and some of them either have no matching test case in b2 or have subjective results.  
  
For example, a BOOST_NO_* macro when not set is easy: the test should compile and run, but when it is set, all we know is that something should fail, but not when (compile, link or run time) and we have no b2 test for that (or CMake before you suggest that either).  We do have a test for that inside a configure script though, and those tests are mostly just to keep that happy.  There are also a few tests which don't fit into a neat pass/fail result, though I suspect most of those are obsolete by now... and finally, all those tests are included by config_test.cpp, the only thing we're not currently testing is that a BOOST_NO_ macro is set when it need not be, or a BOOST_HAS_ macro is not set when it could be.  Even then, the results are sometimes somewhat subjective as some features get disabled on some platforms as "more trouble than their worth" or "have really obscure bugs" even though the basics of the feature do actually work.  
  
BTW for some reason, I appear not to have received any email notifications for this repro for some time... not sure why, they've not gone to spam or to my bin, very strange!
