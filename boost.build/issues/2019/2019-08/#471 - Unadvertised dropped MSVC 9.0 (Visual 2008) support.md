# #471 - Unadvertised dropped MSVC 9.0 (Visual 2008) support [Closed]

> Username: jschueller  
> Created at: 2019-08-26 07:47:52 UTC  
> Updated at: 2021-06-11 05:25:46 UTC  
> Closed at: 2021-06-11 05:25:46 UTC  
> Url: https://github.com/boostorg/build/issues/471  

It seems lib boost.build uses some c++11 features unavailable in vc9:  
```  
build\src\engine\sysinfo.h(41) : error C2864: 'b2::system_info::cpu_core_count_' : only static const integral data members can be initialized within a class  
sysinfo.cpp(10) : fatal error C1083: Cannot open include file: 'thread': No such file or directory  
```  
  
This is not mentioned in the changelog:  
https://www.boost.org/users/history/version_1_71_0.html  
  
Furthermore you mention old msvc versions that are not supported anymore:  
```  
Boost's primary test compilers are:   
...  
Visual C++: 7.1, 8.0, 9.0, 10.0, 11.0, 12.0, 14.0, 14.1   
```  
  
Could you confirm or update if required ?

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:45 UTC  
> Url: https://github.com/boostorg/build/issues/471#issuecomment-859203212  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
