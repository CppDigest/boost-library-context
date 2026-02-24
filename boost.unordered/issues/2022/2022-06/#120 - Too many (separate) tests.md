# #120 - Too many (separate) tests? [Open]

> Username: Flamefire  
> Created at: 2022-06-08 12:18:27 UTC  
> Updated at: 2022-06-08 12:18:27 UTC  
> Url: https://github.com/boostorg/unordered/issues/120  

There may be to many individual test binaries which slow down the build/CI. See e.g. https://github.com/boostorg/unordered/runs/6779803837?check_suite_focus=true  
  
For `b2 -j3 libs/%LIBRARY%/test toolset=msvc-14.3 cxxstd=14,17,20,latest address-model=32,64 variant=debug,release embed-manifest-via=linker` it already yields (at least) 4*2*2=16 variants of each test.  
  
In the test Jamfile we have 54 run-tests and 4 compile-tests yielding at least 928 compilations. That job already takes over an hour on GHA. With coverage collection it is even over 3h and counting.  
  
And it seems the calculation is still to low: `...updated 7154 targets...` O.o  
  
Is it possible to trim that down? E.g. combine more tests into single compilation units as especially on Windows firing up the compiler/linker is very expensive (in terms of time).  
  
Also I'm not sure where the additional factor of 7 (in the targets) is coming from. Maybe that could also be trimmed down somehow.
