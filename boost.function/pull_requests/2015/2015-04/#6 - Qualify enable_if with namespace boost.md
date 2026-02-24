# #6 Qualify enable_if with namespace boost [Merged]

> Username: MarcelRaad  
> Created at: 2015-04-03 07:00:29 UTC  
> Updated at: 2015-04-03 14:03:23 UTC  
> Merged at: 2015-04-03 13:54:46 UTC  
> Closed at: 2015-04-03 13:54:46 UTC  
> Url: https://github.com/boostorg/function/pull/6  

Unfortunately the change from enable_if_c to enable_if in 74c9cc968086e25b6b63342528dd8f3e7f13fa2d broke a lot of other libraries' regression tests on MSVC, which complains about ambiguous symbols.  
  
The failures are visible in http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-test-test-single_header_test-test-msvc-12-0-dbg-adrs-mdl-64-archt-x86-thrd-mlt.html, for example.

---
