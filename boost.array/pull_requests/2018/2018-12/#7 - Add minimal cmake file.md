# #7 Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-12-07 11:01:58 UTC  
> Updated at: 2019-05-01 08:18:41 UTC  
> Merged at: 2019-01-14 17:52:34 UTC  
> Closed at: 2019-01-14 17:52:34 UTC  
> Url: https://github.com/boostorg/array/pull/7  

As announced some time ago (https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M) I'm mkaing a series of PRs that add minimal cmake files to various boost libraries.   
The CMakeLists.txt file in this PR doesn't support Installation, doesn't run the unit-tests and has the sole purpose to ease Integration of individual boost libraries as local submodules in other applications. It is not meant to replace boost build and is not part of Robert's current effort to get a full, boost-wide cmake solution (is this still alive?).  
  
The big advantage is that it is easy to maintain, stays clear of most bike-shedding decisions that should be consistent boost wide and thus will probably change in the future and - last but not least - is available right now.   
  
I also added a travis config file (mostly copied from boost.bind) that will run the boost.array unitt-tests and the cmake-self test (verify, that a local copy of boost.array can now indeed be integrated into a cmake project).  
  
As similar PRs on other boost repositories will depend on this one I'd be gratefull, if I could get early feedback if this PR has any chance to get merged at all.

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-01-08 12:03:40 UTC  
> Url: https://github.com/boostorg/array/pull/7#issuecomment-452275024  

@mclow: Could you please have a look and at least leave a comment if this has any chance of being merged? Of course I'd ideally like this to be merged or given a list of things I need to changed ;)

---
