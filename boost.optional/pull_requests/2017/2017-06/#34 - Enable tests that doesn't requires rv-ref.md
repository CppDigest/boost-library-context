# #34 Enable tests that doesn't requires rv-ref. [Merged]

> Username: Flast  
> Created at: 2017-06-13 11:46:11 UTC  
> Updated at: 2017-06-13 12:24:00 UTC  
> Merged at: 2017-06-13 12:21:28 UTC  
> Closed at: 2017-06-13 12:21:28 UTC  
> Url: https://github.com/boostorg/optional/pull/34  

The test doesn't compile because ifdef switch is inconsistent with main body.  
  
see [Test output: teeks99-09-b-win2012R2-64on64 - optional - optional_test_make_optional / msvc-9.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-09-b-win2012R2-64on64-boost-bin-v2-libs-optional-test-optional_test_make_optional-test-msvc-9-0-dbg-adrs-mdl-64-thrd-mlt.html)  
  
Test on clang 3.8.0 (FreeBSD 11) with both of -std=gnu++98 and -std=gnu++11.

---
