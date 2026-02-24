# #695 - How to change the default toolset during build? [Open]

> Username: yurivict  
> Created at: 2020-12-28 23:02:19 UTC  
> Updated at: 2021-05-29 16:23:03 UTC  
> Url: https://github.com/boostorg/build/issues/695  

@grafikrobot   
  
The build fails:  
```  
  
warning: No toolsets are configured.  
warning: Configuring default toolset "gcc".  
warning: If the default is wrong, your build may not work correctly.  
warning: Use the "toolset=xxxxx" option to override our guess.  
warning: For more configuration options, please consult  
warning: http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html  
/usr/ports/devel/boost_build/work/build-4.3.0/src/tools/gcc.jam:226: in gcc.init from module gcc  
error: toolset gcc initialization:  
error: no command provided, default command 'g++' not found  
error: initialized from  
```  
  
 http://boost.org/boost-build2/doc/html/bbv2/advanced/configuration.html mentioned above is a broken link.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-29 16:00:39 UTC  
> Url: https://github.com/boostorg/build/issues/695#issuecomment-752135480  

The defaults are currently "hard-wired" per platform. Hence the only way to tailor is to configure your actual toolset in a `user-config.jam` or `site-config.jam`. Although now that clang is becoming more prevalent as a system compiler it's worth revisiting smarter selection of the default.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:33 UTC  
> Url: https://github.com/boostorg/build/issues/695#issuecomment-850859622  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
