# #437 - are cached programs stored on the disk? [Closed]

> Username: salmanulhaq  
> Created at: 2015-03-20 17:38:09 UTC  
> Updated at: 2015-03-21 20:00:07 UTC  
> Closed at: 2015-03-21 19:43:16 UTC  
> Url: https://github.com/boostorg/compute/issues/437  

When I insert a built program into program_cache, is it persistent? or just simple key-value in memory cache valid only for as long as the program is running? I'm trying to use it like this:  
1. Get global cache for a given context and check if my built programs are available.  
2. If not, then build and insert into the cache  
3. If available, use them  
  
But every time it fails to fetch them from the cache even though they should be present in the cache after first time, if the cache is persistent.  
  
Maybe I'm missing something here?

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-03-21 16:15:59 UTC  
> Url: https://github.com/boostorg/compute/issues/437#issuecomment-84382364  

The `program_cache` class isn't (currently) persisted on disk. It is just used to store frequently used programs during a single run of the application.  
  
However, Boost.Compute does provide offline program caching through the [`program::build_with_source()`](http://kylelutz.github.io/compute/boost/compute/program.html) method. This will use a hash of the program source to see if the program has been compiled on the system already and, if so, load the binary from disk instead of recompiling it.  
  
So for your use-case, I would keep doing what you have written, but when building the program when it wasn't in the cache, use `program::build_with_source()` instead of `program::create_with_source()`/`program::build()` (and make sure you define the `BOOST_COMPUTE_USE_OFFLINE_CACHE` macro to enable offline program caching).

---

## Comment 2

> Username: salmanulhaq  
> Created at: 2015-03-21 19:43:16 UTC  
> Url: https://github.com/boostorg/compute/issues/437#issuecomment-84442371  

Perfect, thanks!

---

## Comment 3

> Username: keryell  
> Created at: 2015-03-21 19:55:59 UTC  
> Url: https://github.com/boostorg/compute/issues/437#issuecomment-84446503  

It sounds to me like there are some issues to solve: how taking into account external inputs that may be more or less hidden.  
For example, how to deal with #include files in the kernels?  
How to take into account the fact that the user is changing the compiler behaviour (I am thinking to a typical use case of playing on an AMD platform with AMD_OCL_BUILD_OPTION, AMD_OCL_BUILD_OPTIONS_APPEND... environment variables to change compiler & preprocessor options, etc.).  
What if the runtime/driver/compiler is changed?  
Is there a way at least to clean-up the cache?

---

## Comment 4

> Username: salmanulhaq  
> Created at: 2015-03-21 19:59:32 UTC  
> Updated at: 2015-03-21 20:00:07 UTC  
> Url: https://github.com/boostorg/compute/issues/437#issuecomment-84446668  

From what I dug into, it seems that `build_with_source` first computes `sha1` of the source given and then looks it up in the cache directory _(~/.boost_compute for unix for example)_ and if it finds a folder with the computed hash, then it fetches it. Now if **anything** in the source changes, the hash would change and hence it would be built again.  
  
There's no mechanism to clean up the cache though other than manually erasing the root cache directory
