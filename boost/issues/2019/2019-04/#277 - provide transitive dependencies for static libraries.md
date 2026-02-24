# #277 - provide transitive dependencies for static libraries [Open]

> Username: maddanio  
> Created at: 2019-04-11 10:31:40 UTC  
> Updated at: 2019-04-11 10:31:40 UTC  
> Url: https://github.com/boostorg/boost/issues/277  

I just for the first time had cmake pick up boosts own cmake setup. unfortunately this immediately caused problems. unlike cmakes FindBoost.cmake the boost version does not provide transitive dependencies for static libraries.  
```  
target_link_libraries(my_exe Boost::iostreams)  
```  
for example will result in unresolved references to libz if boost is linked statically. also i noticed dynamic and static configs always provide the -mt libraries, wether selected for thread safety or not.
