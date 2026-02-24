# #5 Add scatterv and gatherv collective operations [Closed]

> Username: juliohm  
> Created at: 2014-05-03 15:25:14 UTC  
> Updated at: 2014-06-17 20:24:53 UTC  
> Closed at: 2014-05-03 17:22:06 UTC  
> Url: https://github.com/boostorg/mpi/pull/5  

This patch was originally developed for Boost 1.45.0. It's completely based on the equivalent `scatter.hpp` and `gather.hpp` headers.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-05-03 16:52:28 UTC  
> Url: https://github.com/boostorg/mpi/pull/5#issuecomment-42109981  

A couple of thoughts.  First, this pull request should be against develop, not master.  The merge to master would take place after the code has been debugged and tested in develop.  Second, I don't see any test cases or documentation which are necessary.  Could you please work up some test cases to include in your pull request as well as updating the documentation for these new methods?  It's a good start.

---

## Comment 2

> Username: juliohm  
> Created_at: 2014-05-03 17:05:13 UTC  
> Updated_at: 2014-05-03 17:09:42 UTC  
> Url: https://github.com/boostorg/mpi/pull/5#issuecomment-42110365  

@Belcourt, should I close this pull request then and do another onto develop branch?  
  
Sure, I'll update the documentation. Just need some guidance on where exactly I should write about these two additions.  
  
Currently I'm very busy for adding tests, will try find time in the near future.

---

## Comment 3

> Username: Belcourt  
> Created_at: 2014-05-03 17:13:46 UTC  
> Url: https://github.com/boostorg/mpi/pull/5#issuecomment-42110646  

Yes, you should close this PR and issue another against develop (with examples, tests and doc updates).  And it would probably help if you invested a little energy in learning the Boost development model.  Start here to learn about how Boost uses Github and the development model.  
  
https://svn.boost.org/trac/boost/wiki/ModularBoost  
  
As to the location of Boost MPI tests, please look in the usual test location (libs/mpi/test and libs/mpi/example).  
  
Documentation is here (libs/mpi/doc) and is in quick book format.  The documentation for the current release is here:  
  
http://www.boost.org/doc/libs/1_55_0/doc/html/mpi.html

---

## Comment 4

> Username: juliohm  
> Created_at: 2014-05-03 17:22:06 UTC  
> Url: https://github.com/boostorg/mpi/pull/5#issuecomment-42110885  

Thanks for the link on modular Boost.  
  
Closing this pull request.

---
