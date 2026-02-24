# #201 - which toolset used, when building b2 use TDM-GCC 64bit? gcc, gcc-nocygwin or mingw? [Open]

> Username: zaazbb  
> Created at: 2017-05-31 07:47:44 UTC  
> Updated at: 2017-05-31 17:33:25 UTC  
> Url: https://github.com/boostorg/build/issues/201  

which toolset used, when building b2 use TDM-GCC 64bit?   
gcc, gcc-nocygwin or mingw?  
  
thank you.

---

## Comment 1

> Username: eldiener  
> Created at: 2017-05-31 17:33:25 UTC  
> Url: https://github.com/boostorg/build/issues/201#issuecomment-305260610  

Using mingw or mingw-64 you use the gcc toolset on Windows. Most importantly I have personally found is that the 'bin' directory of the mingw(-64)/gcc implementation must be on the Windows PATH for compiling/linking/testing to work properly, even when a specific toolset definition exists in user-config.jam. For instance my user4-config.jam for mingw-64/gcc-7.1 32-bit looks like:  
  
using gcc : 7.1 :   
    C:/Utilities/mingw-w64/i686-7.1.0-posix-dwarf-rt_v5-rev0/mingw32/bin/g++ :  
    <cxxflags>-Wno-unused-local-typedefs   
    <cxxflags>-ftrack-macro-expansion=0   
    <cxxflags>-Wno-unused-variable   
    ;  
  
and the Windowss PATH has:  
  
C:/Utilities/mingw-w64/i686-7.1.0-posix-dwarf-rt_v5-rev0/mingw32/bin  
  
at the front when I execute:  
  
b2 toolset=gcc-7.1 ...etc.
