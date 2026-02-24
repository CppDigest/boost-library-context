# #8 Add CI framework and move docs and examples into subdirectories [Merged]

> Username: jeking3  
> Created at: 2018-05-29 01:49:13 UTC  
> Updated at: 2018-05-29 01:50:19 UTC  
> Merged at: 2018-05-29 01:50:15 UTC  
> Closed at: 2018-05-29 01:50:15 UTC  
> Url: https://github.com/boostorg/tokenizer/pull/8  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README updated, LICENSE, etc.  
  - Moved documentation into doc/  
  - Moved examples into example/  
  
Build results from my fork:  
  
Appveyor: https://ci.appveyor.com/project/jeking3/tokenizer/build/1.0.11-develop  
Travis CI: https://travis-ci.org/jeking3/tokenizer/builds/384978137  
  
I will assist getting Appveyor, Travis, and Coverity Scan enabled as well.

---
