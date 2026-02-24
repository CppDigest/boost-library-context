# #477 - Cannot find label Config_VC12 when running bootstrap.bat vc12 [Closed]

> Username: Demolishun  
> Created at: 2019-09-04 19:57:49 UTC  
> Updated at: 2020-05-11 00:39:25 UTC  
> Closed at: 2020-05-11 00:39:25 UTC  
> Url: https://github.com/boostorg/build/issues/477  

System:  
Windows 10 Home  
Compiler Visual Studio 2013 Community using the VC12 compiler.  
  
Running from a visual studio 2013 x86 command prompt:  
bootstrap.bat vc12  
  
I would get an error from the config_toolset.bat that the label Config_VC12 could not be found.  When I ran bootstrap.bat vc11 it worked fine.  
  
My workaround (which makes zero sense to me as file size in a goto should not matter) was to put the VC12 config section before the VC11 section:  
  
`:Config_VC12  
if not defined CXX ( set "CXX=cl" )  
if NOT "_%VS120COMNTOOLS%_" == "__" (  
    set "B2_TOOLSET_ROOT=%VS120COMNTOOLS%..\..\VC\"  
    )  
  
if "_%B2_ARCH%_" == "__" set B2_ARCH=x86  
set B2_BUILD_ARGS=%B2_BUILD_ARGS% %B2_ARCH%  
  
if "_%VCINSTALLDIR%_" == "__" call :Call_If_Exists "%B2_TOOLSET_ROOT%VCVARSALL.BAT" %B2_BUILD_ARGS%  
if NOT "_%B2_TOOLSET_ROOT%_" == "__" (  
    if "_%VCINSTALLDIR%_" == "__" (  
        set "PATH=%B2_TOOLSET_ROOT%bin;%PATH%"  
        ) )  
set "B2_CXX=%CXX% /nologo /Zi /MT /TP /Feb2 /wd4996 /Ox /GL"  
set "B2_CXX_LINK=/link kernel32.lib advapi32.lib user32.lib"  
set "_known_=1"  
goto :eof  
  
:Config_VC11  
if not defined CXX ( set "CXX=cl" )  
if NOT "_%VS110COMNTOOLS%_" == "__" (  
    set "B2_TOOLSET_ROOT=%VS110COMNTOOLS%..\..\VC\"  
    )  
if "_%VCINSTALLDIR%_" == "__" call :Call_If_Exists "%B2_TOOLSET_ROOT%VCVARSALL.BAT" %B2_BUILD_ARGS%  
if NOT "_%B2_TOOLSET_ROOT%_" == "__" (  
    if "_%VCINSTALLDIR%_" == "__" (  
        set "PATH=%B2_TOOLSET_ROOT%bin;%PATH%"  
        ) )  
set "B2_CXX=%CXX% /nologo /Zi /MT /TP /Feb2 /wd4996 /Ox /GL"  
set "B2_CXX_LINK=/link kernel32.lib advapi32.lib user32.lib"  
set "_known_=1"  
goto :eof`  
  
I have attached the whole file.  
[config_toolset.bat.zip](https://github.com/boostorg/boost/files/3576250/config_toolset.bat.zip)

---

## Comment 1

> Username: thomas-haller  
> Created at: 2020-04-08 08:50:40 UTC  
> Url: https://github.com/boostorg/build/issues/477#issuecomment-610836044  

I had the same issue (Windows 10, Boost 1.72)  
I guess the problem are the line endings in the .bat files. They are LF and should be CR-LF.  
Changing this in engine/config_toolset.bat solved the problem for me.
