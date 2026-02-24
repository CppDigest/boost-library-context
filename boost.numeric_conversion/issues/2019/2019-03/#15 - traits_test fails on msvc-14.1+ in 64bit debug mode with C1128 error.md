# #15 - traits_test fails on msvc-14.1+ in 64bit debug mode with C1128 error [Closed]

> Username: Kojoley  
> Created at: 2019-03-31 17:09:28 UTC  
> Updated at: 2019-04-18 15:52:43 UTC  
> Closed at: 2019-04-18 15:52:43 UTC  
> Url: https://github.com/boostorg/numeric_conversion/issues/15  

`b2 variant=debug address-model=64 toolset=msvc-14.1 libs/numeric/conversion/test//traits_test`  
```  
boost\libs\numeric\conversion\test\traits_test.cpp : fatal error C1128: number of sections exceeded object file format limit: compile with /bigobj  
```  
  
Either placing `<toolset>msvc:<cxxflags>/bigobj` or `<toolset>msvc:<debug-symbols>off` solves the problem.
