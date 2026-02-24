# #42 Define BOOST_NO_CXX14_CONSTEXPR unless clang > 3.4. [Merged]

> Username: Flast  
> Created at: 2014-10-20 16:27:08 UTC  
> Updated at: 2014-10-21 01:43:23 UTC  
> Merged at: 2014-10-20 17:50:10 UTC  
> Closed at: 2014-10-20 17:50:10 UTC  
> Url: https://github.com/boostorg/config/pull/42  

clang (< 3.5) has a defet with constexpr and dependent type.  
- 3.4 http://melpon.org/wandbox/permlink/xe6bdDS3mPPLhbA3 (fail)  
- 3.5 http://melpon.org/wandbox/permlink/weorcI0Mb9u8g98r  
  
This issue will afect fusion::swap constexpr supporting.  
- [Test output: marshall-mac - fusion - swap / clang-darwin-11](http://www.boost.org/development/tests/develop/developer/output/marshall-mac-boost-bin-v2-libs-fusion-test-swap-test-clang-darwin-11-debug.html)  
  - Above test run under c++11 mode, though c++14 mode is also affected.

---
