# #16 - exception_detail is triggering warning C4265 in visual studio (missing virtual destructor) [Closed]

> Username: Neumann-A  
> Created at: 2018-11-19 14:58:37 UTC  
> Updated at: 2019-03-03 06:15:01 UTC  
> Closed at: 2019-03-03 06:15:01 UTC  
> Url: https://github.com/boostorg/exception/issues/16  

exception_detail has virtual functions but the destructor is not defined virtual and thus triggers this warning

---

## Comment 1

> Username: zajo  
> Created at: 2018-11-19 19:12:10 UTC  
> Url: https://github.com/boostorg/exception/issues/16#issuecomment-440008045  

I need exact command line and compiler version please.

---

## Comment 2

> Username: Neumann-A  
> Created at: 2018-11-20 07:28:34 UTC  
> Url: https://github.com/boostorg/exception/issues/16#issuecomment-440171405  

ah activated that warning explicitly  
  
    `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.16.27023\bin\HostX86\x64\CL.exe /c /IC:\Sources\Repos\Everything\Everything /I"C:\Sources\Extern\vcpkg\installed\x64-windows\include" /Zi /W4 /WX- /diagnostics:classic /Ox /Ob1 /Oi /Ot /Oy /D WIN32 /D _WINDOWS /D NDEBUG /D USE_BOOST_RANDOM /D USE_PCG_RANDOM /D "CMAKE_INTDIR=\"RelWithDebInfo\"" /D _MBCS /Gm- /EHsc /MD /GS /arch:AVX2 /fp:fast /Zc:wchar_t /Zc:forScope /Zc:inline /GR /std:c++17 /Fo"BoostProgrammOptions.dir\RelWithDebInfo\\" /Fd"BoostProgrammOptions.dir\RelWithDebInfo\vc141.pdb" /Gd /TP /FC /errorReport:queue /we4289  /w44640 /w14242 /w14254 /w14263 /w14265 /w14287 /w14296 /w14311 /w14545 /w14546 /w14547 /w14549 /w14555 /w14619 /w14640 /w14826 /w14905 /w14906 /w14928 /bigobj /bigobj C:\Sources\Repos\Everything\Playground\BoostProgramOptions\BoostPOMain.cpp`  
  
warning is activated due to the suggestions in:  
https://lefticus.gitbooks.io/cpp-best-practices/content/02-Use_the_Tools_Available.html
