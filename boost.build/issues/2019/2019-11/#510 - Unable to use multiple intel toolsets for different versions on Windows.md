# #510 - Unable to use multiple intel toolsets for different versions on Windows [Open]

> Username: eldiener  
> Created at: 2019-11-20 02:31:14 UTC  
> Updated at: 2021-05-29 18:22:39 UTC  
> Url: https://github.com/boostorg/build/issues/510  

My user-config.jam has, for Intel on Windows:  
  
```  
using intel : 19.0 : :   
    <compatibility>vc14.2   
    <root>"C:/Utilities/IntelSWTools/parallel_studio_xe_2019/compilers_and_libraries_2019/windows/bin"  
    <rewrite-setup-scripts>off   
    <cxxflags>"/Qdiag-disable:2415,367,3280,177" ;  
      
using intel : 18.0 : :   
    <compatibility>vc14.1   
    <root>"C:/Utilities/IntelSWTools/parallel_studio_xe_2018/compilers_and_libraries_2018/windows/bin"  
    <rewrite-setup-scripts>off   
    <cxxflags>"/Qdiag-disable:2415,367,3280,177,3346" ;  
```  
If I run b2 with toolset=intel-19.0 on some jamfile the process succeeds in running. If I run b2 with toolset=intel-18.0 the process fails because the commands to setup, compile, and link are missing the command names while retaining their parameters.. I have checked the intel-win.init initialization and booth toolsets are initialized properly, each with their own 32 and 64-bit settings for .SETUP, .CC, and .LD etc. with the cpu-conditions matching the toolset name.  
  
If I switch the order of the toolsets above then b2 toolset=intel-18.0 succeeds while b2 toolset=intel-19.0 fails in the same way as above.   
  
I have not been able to discover why in the failing condition .SETUP, .CC, and .LD etc. are empty, after being set properly in intel-win.init initialization. I can debug into the code using the excellent Boost Build debugger but I have no idea how I canm determine why the .SETUP, .CC, and .LD values are empty for the second toolset each time while having their proper values for the first toolset.  
  
This is almost certainly a bug in Boost Build but I have no idea how to fix it so I have reported it here as an issue. Not being able to use multiple intel toolsets on Windows in Boost Build is pretty discouraging, as I can use multiple clang-linux, clang-win, msvc, or gcc toolsets, for different versions of each compiler implementation, without problems.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:09 UTC  
> Url: https://github.com/boostorg/build/issues/510#issuecomment-850877346  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
