# #37 workaround an Open MPI bug: [Merged]

> Username: aminiussi  
> Created at: 2016-11-04 07:24:19 UTC  
> Updated at: 2017-07-24 12:03:08 UTC  
> Merged at: 2017-02-23 02:16:46 UTC  
> Closed at: 2017-02-23 02:16:46 UTC  
> Url: https://github.com/boostorg/mpi/pull/37  

According to:  
http://mpi-forum.org/docs/mpi-3.1/mpi31-report.pdf  
input data on Scatter should be passed as a pointer to const. Open MPI miss that const which invalidate the implicit conversion.  
  
Should fix this one:  
[Sandia-intel-16.0 - mpi - scatter_test-17 / intel-linux](http://www.boost.org/development/tests/develop/developer/output/Sandia-intel-16-0-boost-bin-v2-libs-mpi-test-scatter_test-17-test-intel-linux-debug-threading-multi.html)

---
