# #32 - "use of undeclared identifier 'S_IFREG'" error when using stacktrace.hpp on Alpine linux [Closed]

> Username: GraDKh  
> Created at: 2017-10-26 06:53:44 UTC  
> Updated at: 2017-10-26 19:14:55 UTC  
> Closed at: 2017-10-26 19:14:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/32  

I'm building my application on Apline linux and get the following error in the translation unit that includes stacktrace.hpp:  
  
```  
/root/COExternals/3rdParty/Install/boost-1.60.0-9@icu-55.1-12@openssl-1.0.2h-4-x86_64-static-Clang-4.0.0-Ninja-73006cdeef24/include/boost/stacktrace/detail/safe_dump_posix.ipp:36:63: error: use of undeclared identifier 'S_IFREG'  
    const int fd = ::open(file, O_CREAT | O_WRONLY | O_TRUNC, S_IFREG | S_IWUSR | S_IRUSR);  
                                                              ^  
1 error generated.  
```  
  
I've fixed locally by adding `include <sys/stat.h>` in case when build is on linux platform, but possibly it should be added inside stacktrace library.
