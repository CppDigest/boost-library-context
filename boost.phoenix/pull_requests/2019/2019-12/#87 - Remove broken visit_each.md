# #87 Remove broken visit_each [Merged]

> Username: Kojoley  
> Created at: 2019-12-04 17:39:39 UTC  
> Updated at: 2019-12-06 14:23:18 UTC  
> Merged at: 2019-12-06 14:11:38 UTC  
> Closed at: 2019-12-06 14:11:39 UTC  
> Url: https://github.com/boostorg/phoenix/pull/87  

The test for it is not run by the test suit, it does not compile because of missing includes somewhere, and fixing compilation is not the end of the story. The `visit_each` seems to be simply never worked, running the test fails with a wrong result.  
  
The only potential usage of `visit_each` is the Signals1/2 library, and I did not find any bug report about that.

---
