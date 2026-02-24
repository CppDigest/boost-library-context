# #713 - build with vs 2017_xp。 [Closed]

> Username: mecctor  
> Created at: 2021-02-23 10:54:42 UTC  
> Updated at: 2021-02-23 13:15:44 UTC  
> Closed at: 2021-02-23 13:15:44 UTC  
> Url: https://github.com/boostorg/build/issues/713  

.\b2.exe -j5 --toolset=msvc-14.1_xp --with-date_time --with-filesystem --with-system --with-thread --with-regex link=static runtime-link=static  
I build the boost with the command. my software use the 	shared_memory_object of boost. and it work well on Win7,Win10.  
but i can not work on WinXP,It crach.......

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-02-23 13:15:44 UTC  
> Url: https://github.com/boostorg/build/issues/713#issuecomment-784194900  

Please report your issue to the specific Boost library you are having a problem with. This issue tracker is for the build system only.
