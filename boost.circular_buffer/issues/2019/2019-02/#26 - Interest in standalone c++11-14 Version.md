# #26 - Interest in standalone c++11/14 Version? [Closed]

> Username: Mike-Devel  
> Created at: 2019-02-26 19:00:47 UTC  
> Updated at: 2019-02-28 05:14:32 UTC  
> Closed at: 2019-02-28 05:14:32 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/26  

Is there any interest in PRs that would turn  this into a standalone (but c++14 - maybe 11 only) library?  
  
I was using circular buffer in a side project of mine and as it was the only direct boost dependency I was curious if I could remove the boost dependency completely without rewriting the library.  
  
Turns out that it is fairly easy to do this via a couple of search&replace transformations and 2-3 internalizations, because what few boost types are used have mostly been merged into the standard library.  
  
I didn't need compatibility with any compiler that doesn't have full c++14 support,  but if there is interest in this at all, I can check if c++11 would be sufficient (available type traits might be a limitation).

---

## Comment 1

> Username: glenfe  
> Created at: 2019-02-26 20:08:53 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/26#issuecomment-467593529  

> Is there any interest in PRs that would turn this into a standalone (but c++14 - maybe 11 only) library?  
  
Requiring C++11 or higher would break existing C++03 users who upgrade to newest Boost versions. (For CircularBuffer, I know at least one such user).  
  
Reducing the Boost dependencies of CircularBuffer, while still retaining compatibility with C++03, is worthwhile. Either conditionally reducing those dependencies (e.g. use C++11 facilities if available), or unconditionally.  
  
Currently those dependencies are: assert, concept_check, config, core, move, static_assert, throw_exception, type_traits  
  
Which of those would you want to drop (again, while not breaking C++03 users)?

---

## Comment 2

> Username: Mike-Devel  
> Created at: 2019-02-27 07:40:04 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/26#issuecomment-467756789  

For me it is more an all or nothing kind of question: Having no depencies at all has some value, but if there are any boost internal dependencies  at all, it makes little difference to me how many there are (at least on the level of Boost.CircularBuffer I didn't have any trouble with boost libs in the last years).   
  
If you do want to remove individual dependencies (conditionally or unconditionally), it may be worthwhile to check if any of them have a significant impact on compiletimes. As an anecdotal data point I can say that compiling the examples with my standalone fork of BoostCicularBuffer took only about two thirds the time than with the main version (same example code in both cases without boost dependencies) on wsl with gcc7 in debug mode. However, I didn't investigate any further,  as that wasn't my main concern and it may well be that the AV was interfering at the time, which is known to significantly slow down compilation on WSL. So take this with a big spoon of salt.
