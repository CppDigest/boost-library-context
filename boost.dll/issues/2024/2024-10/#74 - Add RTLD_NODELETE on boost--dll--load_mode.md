# #74 - Add RTLD_NODELETE on boost::dll::load_mode [Closed]

> Username: tgmatos  
> Created at: 2024-10-28 15:57:53 UTC  
> Updated at: 2025-01-16 16:08:48 UTC  
> Closed at: 2025-01-16 16:08:28 UTC  
> Url: https://github.com/boostorg/dll/issues/74  

>RTLD_NODELETE (since glibc 2.2)  
    Do not unload the library during dlclose(). Consequently, the library's static variables are not reinitialized if the library is reloaded with dlopen() at a later time. This flag is not specified in POSIX.1-2001.   
  
`RTLD_NODELETE` is available on Linux and BSD systems.
