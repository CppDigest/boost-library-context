# #976 - Windows BOOST build broken [Closed]

> Username: tjcw  
> Created at: 2024-11-05 13:36:48 UTC  
> Updated at: 2024-11-05 15:50:59 UTC  
> Closed at: 2024-11-05 15:50:59 UTC  
> Url: https://github.com/boostorg/boost/issues/976  

I checked out all the boost repos 'develop' branch a few days ago (1 Nov 2024, I think) and built it on various platforms. Alll but Windows succeeded; the Windows build failed because of missing include files, which seemed to be because 'b2' thought that Windows supported symbolic links.  
  
I have refreshed the source code at today's 'develop' branch and will report whether I currently see the same issue.

---

## Comment 1

> Username: tjcw  
> Created at: 2024-11-05 15:22:40 UTC  
> Url: https://github.com/boostorg/boost/issues/976#issuecomment-2457460973  

Yes, the error still occurs with today's checkout  
```  
Bootstrapping is done. To build, run:  
  
    .\b2  
  
    To generate header files, run:  
  
    .\b2 headers  
  
To adjust configuration, edit 'project-config.jam'.  
Further information:  
  
    - Command line help:  
    .\b2 --help  
       
    - Getting started guide:   
    http://boost.org/more/getting_started/windows.html  
       
    - Boost.Build documentation:  
    http://www.boost.org/build/  
  
--   <boost> Building Boost  
Performing configuration checks  
  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
  
```
