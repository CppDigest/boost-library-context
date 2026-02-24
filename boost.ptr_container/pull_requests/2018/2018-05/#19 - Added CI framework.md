# #19 Added CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-27 13:21:37 UTC  
> Updated at: 2018-05-27 13:59:02 UTC  
> Merged at: 2018-05-27 13:58:58 UTC  
> Closed at: 2018-05-27 13:58:58 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/19  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
Discovered: UBSAN issues (https://github.com/boostorg/ptr_container/issues/18)  
  
Build results:  
  
Appveyor: https://ci.appveyor.com/project/jeking3/ptr-container/build/1.0.2-develop  
Travis: https://travis-ci.org/jeking3/ptr_container/builds/384348784  
(note: the UBSAN build was disabled and issue above added to track)

---
