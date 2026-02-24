# #252 - -fsplit-stack flag and AIX [Closed]

> Username: EGuesnet  
> Created at: 2020-06-23 10:36:32 UTC  
> Updated at: 2020-07-17 05:53:59 UTC  
> Closed at: 2020-07-17 05:53:59 UTC  
> Url: https://github.com/boostorg/fiber/issues/252  

Hi,  
I am trying to port Boost on AIX.  
  
OS: AIX 7.1  
Compiler: GCC 8.4  
  
I have reported an issue about -fsplit-stack on context library. Fiber library is also concerned. I create this issue to inform you, but it will be easier if the discussion will be centralized at https://github.com/boostorg/context/issues/143.  
  
TL;DR: the -fsplit-stack flag seems to be available only on Linux, and it is used for all OS by Boost.
