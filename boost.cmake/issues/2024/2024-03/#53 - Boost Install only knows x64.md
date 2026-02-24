# #53 - Boost Install only knows x64 [Closed]

> Username: Neumann-A  
> Created at: 2024-03-10 22:45:40 UTC  
> Updated at: 2024-04-20 14:17:12 UTC  
> Closed at: 2024-04-20 14:17:12 UTC  
> Url: https://github.com/boostorg/cmake/issues/53  

https://github.com/boostorg/cmake/blob/7276e8055d0faab429b11983eccddc68fee075f1/include/BoostInstall.cmake#L155-L156  
  
Either the build should fail or the missing suffixes should be added by checking `CMAKE_SYSTEM_PROCESSOR`.
