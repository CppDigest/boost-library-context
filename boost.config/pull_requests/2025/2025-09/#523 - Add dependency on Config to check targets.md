# #523 Add dependency on Config to check targets [Merged]

> Username: grisumbras  
> Created at: 2025-09-22 18:02:13 UTC  
> Updated at: 2025-09-23 08:37:47 UTC  
> Merged at: 2025-09-23 08:33:32 UTC  
> Closed at: 2025-09-23 08:33:32 UTC  
> Url: https://github.com/boostorg/config/pull/523  

The targets used for config checks do not have a dependency on `/boost/config//boost_config`. This is not a problem when  the files are used as a part of the actual Boost.Config project. But consider this use case:  
  
1. Boost.Config is installed on the user's machine, including its headers and b2 extension modules.  
2. A Jam project that represents `/boost/config` is generated for the user.  
3. A `-config.jam` file is generated for the user that points to the generated project via   
    ```  
    project-search /boost/config : path/to/boost/config/installation ;  
    ```  
4. User's own jam files use Boost.Config's checks e.g.  
    ```  
    import-search /boost/config/checks ;  
    import config ;  
      
    lib mylib : mylib.cpp : [ config.requires cxx11_constexpr ] ;  
    ```  
  
In this case Boost.Config's `build.jam` is not used and so, the check targets cannot find the necessary headers.  
  
This use case is how Conan package manager works. So, the change is aimed to provide support for modular build to Boost libraries using Conan.

---
