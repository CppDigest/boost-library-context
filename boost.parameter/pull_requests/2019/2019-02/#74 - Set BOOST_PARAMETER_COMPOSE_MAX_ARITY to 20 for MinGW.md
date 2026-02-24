# #74 Set BOOST_PARAMETER_COMPOSE_MAX_ARITY to 20 for MinGW [Merged]

> Username: CromwellEnage  
> Created at: 2019-02-14 13:38:15 UTC  
> Updated at: 2019-04-27 18:43:13 UTC  
> Merged at: 2019-04-27 11:57:11 UTC  
> Closed at: 2019-04-27 11:57:11 UTC  
> Url: https://github.com/boostorg/parameter/pull/74  

In my GitHub fork of Boost.Graph, I've been upgrading some algorithms to use BOOST_PARAMETER_FUNCTION, but MinGW with GCC 5.3.0 has been running out of heap space on some of the affected tests.  The goal of this commit is to eliminate these occurrences.

---
