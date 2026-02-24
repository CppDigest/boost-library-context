# #34 Fix hidden visibility symbols for mac/clang [Merged]

> Username: memem359  
> Created at: 2017-06-07 21:53:09 UTC  
> Updated at: 2017-06-27 15:32:45 UTC  
> Merged at: 2017-06-27 15:32:45 UTC  
> Closed at: 2017-06-27 15:32:45 UTC  
> Url: https://github.com/boostorg/iostreams/pull/34  

The symbol visibility settings are only set for Win/gcc builds. Added equivalent instructions that will work for Mac OS/Clang builds. This is similar to what was done in the config files of the other Boost libraries.

---
