# #76 Add minimal cmake support [Merged]

> Username: Mike-Devel  
> Created at: 2019-04-15 10:37:27 UTC  
> Updated at: 2019-04-30 13:37:10 UTC  
> Merged at: 2019-04-30 12:54:02 UTC  
> Closed at: 2019-04-30 12:54:02 UTC  
> Url: https://github.com/boostorg/parameter/pull/76  

Functionality:  
  
- CMake file only supports add_subdirectory workflow.  
- Provides target `Boost::parameter` to "link" against (effectively providing the include directory of parameter and information about its dependencies).  
- Allows compilation and running of most of the unit tests.   
- Doesn't support installation (That is taken care of via b2).  
  
For basic testing:  
  
1) Make sure you have checked out the latest (develop) version of all other boost libraries you are depending on (directly or indirectly) into sibling folders of this library (e.g. recursively check out branch develop of boostorg/boost).  
  
2) Copy a file like the one in: https://github.com/boostorg/boost/pull/193/files into the boost root directory. Essentially, that file will simply call `add_subdirectory( )` on all boost libraries with a cmake file to make sure all dependencies are known to cmake. However, it will also ignore some libraries that are known to be troublesome with the add_subdirectory workflow.  
  
3) Then (from boost root)  
  
      mkdir __build__   
      cd __build__  
      cmake -DBOOST_PARAMETER_INCLUDE_TESTS=ON ..  
      cmake --build .  
      ctest .

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2019-04-30 13:37:10 UTC  
> Url: https://github.com/boostorg/parameter/pull/76#issuecomment-487955120  

Thanks

---
