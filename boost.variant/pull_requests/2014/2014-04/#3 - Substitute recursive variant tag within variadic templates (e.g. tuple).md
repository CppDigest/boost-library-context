# #3 Substitute recursive variant tag within variadic templates (e.g. tuple) [Merged]

> Username: ecatmur  
> Created at: 2014-04-11 00:51:18 UTC  
> Updated at: 2014-04-24 07:59:45 UTC  
> Merged at: 2014-04-11 10:20:37 UTC  
> Closed at: 2014-04-11 10:20:37 UTC  
> Url: https://github.com/boostorg/variant/pull/3  

Follow up to https://github.com/boostorg/variant/pull/2 (since we still can't change target branch of a pull request):  
  
Change target branch to develop  
  
Guard tests using `<tuple>` with `!defined(BOOST_NO_CXX11_HDR_TUPLE)`

---

## Comment 1

> Username: apolukhin  
> Created_at: 2014-04-11 11:40:52 UTC  
> Url: https://github.com/boostorg/variant/pull/3#issuecomment-40194813  

Thanks for providing this patch and tests!

---

## Comment 2

> Username: apolukhin  
> Created_at: 2014-04-24 07:59:45 UTC  
> Url: https://github.com/boostorg/variant/pull/3#issuecomment-41253305  

This feature was disabled for MSVC-12 and some old GCC versions (4.6 and before) in commit https://github.com/boostorg/variant/commit/9bc9c1c143bf64e4c50dff1d89690d0bab6e7d4e.  
  
Without that commit regression tests for MSVC-12 were failing.

---
