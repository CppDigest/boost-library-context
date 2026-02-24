# #85 Qualify calling of as_const. [Merged]

> Username: Flast  
> Created at: 2017-02-04 04:28:58 UTC  
> Updated at: 2018-04-16 11:16:06 UTC  
> Merged at: 2018-04-13 12:21:59 UTC  
> Closed at: 2018-04-13 12:21:59 UTC  
> Url: https://github.com/boostorg/graph/pull/85  

It is ambiguous with upcoming standardized `std::as_const`.  
  
see [teeks99-09f-win2012R2-64on64 - graph - test_graphs / msvc-14.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-09f-win2012R2-64on64-boost-bin-v2-libs-graph-test-test_graphs-test-msvc-14-0-dbg-adrs-mdl-64-thrd-mlt.html) and [Flast-FreeBSD11 - graph - test_graphs / clang-linux-3.8~gnu++1z](http://www.boost.org/development/tests/develop/developer/output/Flast-FreeBSD11-boost-bin-v2-libs-graph-test-test_graphs-test-clang-linux-3-8~gnu++1z-debug.html).

---
