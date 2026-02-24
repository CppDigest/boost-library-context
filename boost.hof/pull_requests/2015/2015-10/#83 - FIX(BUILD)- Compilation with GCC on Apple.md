# #83 FIX(BUILD): Compilation with GCC on Apple [Merged]

> Username: bcachet  
> Created at: 2015-10-07 18:51:05 UTC  
> Updated at: 2015-10-07 20:27:31 UTC  
> Merged at: 2015-10-07 20:27:31 UTC  
> Closed at: 2015-10-07 20:27:31 UTC  
> Url: https://github.com/boostorg/hof/pull/83  

I got error `g++-5: error: unrecognized command line option '-stdlib=libc++'` when compiling tests on Apple with GNU GCC.  
  
I added a test inside CMakeLists.txt to only add `stdlib=libc++` compilation flags when on Apple but not when using GNU GCC: (NOT CMAKE_COMPILER_IS_GNUCXX)

---

## Comment 1

> Username: pfultz2  
> Created_at: 2015-10-07 20:24:05 UTC  
> Url: https://github.com/boostorg/hof/pull/83#issuecomment-146317431  

Thanks, it might be better to check if the flags is valid instead(like how it is done for the `-std=*` flags), but this will work for now. Thanks

---
