# #187 Add Boost.Build support for the unit tests [Merged]

> Username: ldionne  
> Created at: 2015-10-09 17:37:46 UTC  
> Updated at: 2015-10-20 17:24:16 UTC  
> Merged at: 2015-10-20 15:28:22 UTC  
> Closed at: 2015-10-20 15:28:22 UTC  
> Url: https://github.com/boostorg/hana/pull/187  

This should allow running Hana's tests as part of the whole Boost distribution, which is a requirement for Hana to be shipped with Boost 1.60.  
  
If we provide a Boost.Build-based build system, Boost's regression matrix would be updated automatically by the usual testers, so that would fix #184.  
  
In the current state of affairs, however, the `Jamfile` does not test everything that's being tested by the CMake suite, because setting up everything would be time consuming.

---
