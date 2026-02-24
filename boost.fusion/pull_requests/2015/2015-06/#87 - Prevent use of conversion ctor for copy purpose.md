# #87 Prevent use of conversion ctor  for copy purpose. [Merged]

> Username: Flast  
> Created at: 2015-06-30 17:28:16 UTC  
> Updated at: 2015-07-01 15:25:09 UTC  
> Merged at: 2015-06-30 23:05:05 UTC  
> Closed at: 2015-06-30 23:05:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/87  

Some compilers elect conversion ctor for copy purpose in implicitly defined copy ctor with derived class.  
  
It will fix: [graph - mcgregor_subgraphs_test / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-graph-test-mcgregor_subgraphs_test-test-msvc-10-0-dbg-adrs-mdl-64-thrd-mlt.html), [graph - vf2_sub_graph_iso_test / msvc-10.0](http://www.boost.org/development/tests/develop/developer/output/teeks99-08c-win2012R2-64on64-boost-bin-v2-libs-graph-test-vf2_sub_graph_iso_test-test-msvc-10-0-dbg-adrs-mdl-64-thrd-mlt.html) and so on ...  
  
see: http://lists.boost.org/boost-users/2015/06/84496.php

---
