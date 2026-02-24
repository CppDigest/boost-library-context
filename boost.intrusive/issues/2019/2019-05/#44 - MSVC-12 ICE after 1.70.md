# #44 - MSVC-12 ICE after 1.70 [Closed]

> Username: Lastique  
> Created at: 2019-05-26 16:35:12 UTC  
> Updated at: 2019-06-08 21:16:10 UTC  
> Closed at: 2019-06-08 21:16:10 UTC  
> Url: https://github.com/boostorg/intrusive/issues/44  

This manifests as compilation errors in Boost.Log, for example:  
  
```  
C:\src\boost\boost/intrusive/list_hook.hpp(92) : fatal error C1001: An internal error has occurred in the compiler.  
(compiler file 'msc1.cpp', line 1325)  
 To work around this problem, try simplifying or changing the program near the locations listed above.  
Please choose the Technical Support command on the Visual C++   
 Help menu, or open the Technical Support help file for more information  
        C:\src\boost\boost/log/sinks/block_on_overflow.hpp(60) : see reference to class template instantiation 'boost::intrusive::list_base_hook<boost::intrusive::link_mode<auto_unlink>>' being compiled  
Internal Compiler Error in C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\BIN\amd64\cl.exe.  You will be prompted to send an error report to Microsoft later.  
  
    call "bin.v2\standalone\msvc\msvc-12.0\address-model-64\architecture-x86\msvc-setup.bat" amd64 >nul  
 cl /Zm800 -nologo @"bin.v2\libs\log\test\~hdr-sinks~hpp.test\msvc-12.0\debug\address-model-64\link-static\threadapi-win32\threading-multi\~hdr-sinks~hpp.obj.rsp"   
```  
  
https://ci.appveyor.com/project/Lastique/log/builds/24810593/job/bb94dmla8d0a5a5b#L739  
  
Reproduces with MSVC-12 x86 and x64 but not other MSVC versions.  
  
Checking out `boost-1.70.0` of Boost.Intrusive and keeping everything else unchanged resolves these errors.
