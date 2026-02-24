# #41 Fix error on __has_include with older clang (< 3.1) [Merged]

> Username: Flast  
> Created at: 2014-10-14 04:03:27 UTC  
> Updated at: 2014-10-14 09:56:40 UTC  
> Merged at: 2014-10-14 07:54:04 UTC  
> Closed at: 2014-10-14 07:54:04 UTC  
> Url: https://github.com/boostorg/config/pull/41  

Older clang (< 3.1) rejects __has_include(header), therefore use __has_include(&lt;header&gt;) instead.  
  
see [Test output: teeks99-03f-Ubuntu12.04-64 - config - abi_test / clang-linux-3.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-03f-Ubuntu12-04-64-boost-bin-v2-libs-config-test-abi_test-test-clang-linux-3-0-debug.html)

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-10-14 07:53:59 UTC  
> Url: https://github.com/boostorg/config/pull/41#issuecomment-59002729  

Oops, my bad - fat fingers again!

---
