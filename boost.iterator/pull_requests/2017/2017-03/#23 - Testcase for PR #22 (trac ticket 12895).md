# #23 Testcase for PR #22 (trac ticket 12895) [Merged]

> Username: morinmorin  
> Created at: 2017-03-15 11:57:22 UTC  
> Updated at: 2017-03-16 00:09:03 UTC  
> Merged at: 2017-03-15 16:19:14 UTC  
> Closed at: 2017-03-15 16:19:14 UTC  
> Url: https://github.com/boostorg/iterator/pull/23  

Before applying PR #22: this testcase fails to compile.  
After: it runs successfully.  
(Note that it fails to compile only if the TR1-style `boost::result_of` is used — i.e. compiled in a C++03 mode or `BOOST_RESULT_OF_USE_TR1` is defined.)  
  
Adapted from Daniel's testcase in trac ticket 12895.

---
