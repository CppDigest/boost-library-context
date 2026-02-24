# #155 118 boost integration [Merged]

> Username: anarthal  
> Created at: 2023-10-05 12:11:16 UTC  
> Updated at: 2023-10-10 20:34:35 UTC  
> Merged at: 2023-10-05 20:42:32 UTC  
> Closed at: 2023-10-05 20:42:32 UTC  
> Url: https://github.com/boostorg/redis/pull/155  

This addresses what's missing to make this library Boost compliant:  
  
* Documentation is now built using `b2` target `boostrelease`, via Doxygen, and copied to the standard `doc/html` location.  
* Added documentation redirection file.  
* Updated documentation to match Boost conventions.  
* Updated `CMakeLists.txt` to comply with what's recommended by `boostdep --cmake redis`.  
* CMake now doesn't fail if OpenSSL is not found when running from the superproject.  
* Added `test/cmake_test`.  
* Updated the CMake CI to test the CMake workflows recommended by Boost.CI, as well as some other workflows I've found relevant in other libraries.  
* Added `test/Jamfile`.  
* Added CI jobs to test B2 builds.  
* CI workflows now test with the `master` and `develop` branches of Boost, instead of with pre-built Boost distributions.  
* Added `meta/libraries.json`.  
* Fixed some documentation issues.  
* Fixed a Windows link error due to Boost.Test.  
* Renamed `examples` and `tests` folders to `example` and `test` to match the Boost requirements.  
  
close #118

---

## Comment 1

> Username: mzimbres  
> Created_at: 2023-10-05 20:42:05 UTC  
> Url: https://github.com/boostorg/redis/pull/155#issuecomment-1749612311  

Awesome. It would have taken weeks for me to do this job.

---
