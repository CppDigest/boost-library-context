# #8 Fix test error in msvc-10.0 [Merged]

> Username: Flast  
> Created at: 2014-06-12 04:06:53 UTC  
> Updated at: 2014-06-14 07:21:26 UTC  
> Merged at: 2014-06-12 09:59:44 UTC  
> Closed at: 2014-06-12 09:59:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/8  

My previous PR(#7)'s test will occur compile error in msvc-10.0, since it doesn't support variadic templates.  
Thus, entire test should be ignored if variadic templates isn't supported.  
  
failed test: http://www.boost.org/development/tests/develop/developer/output/teeks99-02c-win2008-64on64-boost-bin-v2-libs-fusion-test-std_tuple-test-msvc-10-0-debug-threading-multi.html

---

## Comment 1

> Username: djowel  
> Created_at: 2014-06-12 10:16:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/8#issuecomment-45851613  

thanks for the fix!

---
