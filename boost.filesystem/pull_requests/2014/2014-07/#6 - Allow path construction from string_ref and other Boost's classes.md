# #6 Allow path construction from string_ref and other Boost's classes [Closed]

> Username: apolukhin  
> Created at: 2014-07-30 09:46:06 UTC  
> Updated at: 2014-08-25 20:28:53 UTC  
> Closed at: 2014-08-25 20:28:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/6  



---

## Comment 1

> Username: Beman  
> Created_at: 2014-08-25 20:28:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/6#issuecomment-53324934  

Antony,  
  
For the TS version of filesystem, which is under development and should ship in boost 1.57, the class path Source dispatching is being upgraded to depend on type traits, so there is no need for changes to is_pathable<>.  
  
The path_unit_test.cpp test cases are quite useful, however, and I've added them from your pull request. That's commit [feature/ts-path-conversion 93b8254]. I'm happy to report they all passed first try on Windows/VC++ 2012 and Linux/4.gcc 4.8.2.  
  
Thanks,  
  
--Beman

---
