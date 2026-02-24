# #4 Added CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-19 02:57:15 UTC  
> Updated at: 2018-05-27 13:37:53 UTC  
> Merged at: 2018-05-27 13:37:48 UTC  
> Closed at: 2018-05-27 13:37:48 UTC  
> Url: https://github.com/boostorg/property_map/pull/4  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
Builds from my fork:  
  
Appveyor: https://ci.appveyor.com/project/jeking3/property-map/build/1.0.4-develop  
Travis: https://travis-ci.org/jeking3/property_map/builds/380948207  
  
After applying this PR we need to enable Appveyor and Travis CI for this repository.  Ideally, we enable them before and force push to kick it off here as proof it works in this repo.

---
