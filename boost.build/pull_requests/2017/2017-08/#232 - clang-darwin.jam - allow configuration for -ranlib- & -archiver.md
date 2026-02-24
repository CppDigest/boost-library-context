# #232 clang-darwin.jam - allow configuration for <ranlib> & <archiver> [Merged]

> Username: tallavi  
> Created at: 2017-08-28 08:18:52 UTC  
> Updated at: 2021-10-02 22:08:38 UTC  
> Merged at: 2017-08-31 15:54:45 UTC  
> Closed at: 2017-08-31 15:54:45 UTC  
> Url: https://github.com/boostorg/build/pull/232  

When compiling in MacOS with clang, it was not possible to customize the path for **ranlib** and **ar**.  
  
This PR allows to customize the paths by modifying user-config.jam:  
  
```  
<archiver>{CUSTOM_TOOLCHAIN_PATH}/arm_16_libc++/bin/arm-linux-androideabi-ar  
<ranlib>{CUSTOM_TOOLCHAIN_PATH}/arm_16_libc++/bin/arm-linux-androideabi-ranlib  
```  
  
The default behavior was not changed.

---
