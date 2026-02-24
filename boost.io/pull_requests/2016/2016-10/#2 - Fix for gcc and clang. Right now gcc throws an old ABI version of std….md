# #2 Fix for gcc and clang. Right now gcc throws an old ABI version of std… [Merged]

> Username: eldiener  
> Created at: 2016-10-27 15:29:26 UTC  
> Updated at: 2017-01-29 23:23:40 UTC  
> Merged at: 2017-01-29 23:23:40 UTC  
> Closed at: 2017-01-29 23:23:40 UTC  
> Url: https://github.com/boostorg/io/pull/2  

…::ios_base::failure even when the version being used is the newer ABI version of std::ios_base::failure. I have been told that this will be fixed in gcc-7.

---
