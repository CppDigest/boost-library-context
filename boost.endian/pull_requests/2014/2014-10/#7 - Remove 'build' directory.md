# #7 Remove 'build' directory [Closed]

> Username: jhunold  
> Created at: 2014-10-17 17:40:21 UTC  
> Updated at: 2015-01-05 14:23:25 UTC  
> Closed at: 2015-01-05 14:23:25 UTC  
> Url: https://github.com/boostorg/endian/pull/7  

As the "build" dfirectory is picked up by the top-level build rule,  
the two test executables are added to the build. The explicit target  
property "<asynch-exceptions>on" and the dependency on Boost.System  
(via Boost.Timer -> Boost.Chrono) leads to conflicting target types  
when building named libraries for staging.  
The error message is:  
  
error: Name clash for '<pE:\dev\devel\boost\stage\lib>libboost_system-vc120-mt-1_57.lib'  
error:   
error: Tried to build the target twice, with property sets having   
error: these incompabile properties:  
error:   
error:     -  <asynch-exceptions>off  
error:     -  <asynch-exceptions>on  
error:   
error: Please make sure to have consistent requirements for these   
error: properties everywhere in your project, especially for install  
error: targets.  
  
The proposed solution is to move the build rules to the test directory  
and making them "explicit" as to not interfere with the testing  
system. Another solution would be to move the tests to a separete  
"benchmark" directory.

---

## Comment 1

> Username: Beman  
> Created_at: 2015-01-05 14:23:25 UTC  
> Url: https://github.com/boostorg/endian/pull/7#issuecomment-68713562  

Done. Renamed the directory "benchmark" per your suggestion.  
  
Thanks,  
  
--Beman

---
