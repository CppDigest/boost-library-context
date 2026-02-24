# #45 Workarounds for MSVC [Merged]

> Username: Flast  
> Created at: 2014-11-25 15:37:07 UTC  
> Updated at: 2014-11-26 00:43:42 UTC  
> Merged at: 2014-11-25 23:13:44 UTC  
> Closed at: 2014-11-25 23:13:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/45  

Here are two workarounds for MSVC.  
- First (687668c), it affects VS2015 Preview only, but I believe there is no performance, syntactic and any other regressions. Thus it can be merged straightforward.  
  - will fix [adapt_adt / msvc-14.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08f-win2012R2-64on64-boost-bin-v2-libs-fusion-test-adapt_adt-test-msvc-14-0-debug-threading-multi.html) and so on ...  
- Second <del>(2c64426)</del>(9ab7774, comment updated and no other changes), it affects all of rvalue-reference available MSVC (>= 10.0). For more details, see [inline comment](https://github.com/Flast/fusion/blob/2c6442682e080df06d535c5d3c2beed43ce4651b/include/boost/fusion/support/config.hpp#L44-L68).  
  - will fix [map_comparison / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-fusion-test-map_comparison-test-msvc-10-0-debug-threading-multi.html), [map_construction / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-fusion-test-map_construction-test-msvc-10-0-debug-threading-multi.html) and so on ...  
  
Tested compilers  
- GCC 4.9.2 (c++98, c++11, c++14 mode)  
- Clang 3.5.0 (c++98, c++11, c++14 mode)  
- MSVC  
  - 8.0 (2005 EE)  
  - 9.0 (2008 Pro)  
  - 10.0 (2010 EE)  
  - 11.0 (2012 EE)  
  - 12.0 (2013 EE)  
  - 14.0 (VS 2015 Preview)

---

## Comment 1

> Username: Flast  
> Created_at: 2014-11-25 17:02:48 UTC  
> Url: https://github.com/boostorg/fusion/pull/45#issuecomment-64433700  

I'll push one more workaround about MSVC 8.0's lookup failure bug...

---

## Comment 2

> Username: Flast  
> Created_at: 2014-11-25 17:06:46 UTC  
> Updated_at: 2014-11-26 00:43:35 UTC  
> Url: https://github.com/boostorg/fusion/pull/45#issuecomment-64434389  

Third (fbbfc16) will fix lookup failure about `from_sequence_convertible_type` like [adapt_assoc_adt_named / msvc-8.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08a-win2012R2-64on64-boost-bin-v2-libs-fusion-test-adapt_assoc_adt_named-test-msvc-8-0-debug-threading-multi.html).

---

## Comment 3

> Username: djowel  
> Created_at: 2014-11-25 23:14:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/45#issuecomment-64488801  

Amazing work as usual, Kohei! You rock!

---
