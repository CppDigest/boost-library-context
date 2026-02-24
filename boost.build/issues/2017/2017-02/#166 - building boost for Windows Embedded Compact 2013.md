# #166 - [WEC] building boost for Windows Embedded Compact 2013 [Open]

> Username: frederich  
> Created at: 2017-02-20 07:34:15 UTC  
> Updated at: 2017-02-20 07:39:52 UTC  
> Url: https://github.com/boostorg/build/issues/166  

I want to use Boost.Dll under Windows Embbedded Compact 2013, but I've to build the Boost.System for WEC to do so.   
  
https://github.com/boostorg/dll/issues/10  
https://github.com/boostorg/winapi/issues/38  
https://svn.boost.org/trac/boost/ticket/12853#comment:1  
  
Normally I build static boost with the following commands:  
```  
bootstrap.bat  
.\b2.exe headers  
.\b2.exe variant=release,debug link=static runtime-link=static threading=multi address-model=32 architecture=x86 --toolset=msvc-15.0 --without-python --without-mpi --stagedir=stage stage  
```  
  
The WEC SDK 2013 comes bundled with a Microsoft Toolchain (VS 2012 based) like the Windows 7.1 SDK.  
What are correct `--toolset=` settings for the build?
