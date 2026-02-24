# #490 - [ERROR] Bootstrap.bat on win10 [Open]

> Username: Romain-P  
> Created at: 2019-10-06 21:55:17 UTC  
> Updated at: 2021-05-29 18:22:44 UTC  
> Url: https://github.com/boostorg/build/issues/490  

I cloned this repository and tried to execute the bootstrap.bat.  
I'm getting the following error:  
```  
LOCALAPPDATA=C:\Users\romain\AppData\Local  
Found with vswhere D:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools  
Found with vswhere D:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools  
Call_If_Exists "D:\Program Files (x86)\Microsoft Visual Studio\2017\BuildTools\Common7\Tools\..\..\VC\Auxiliary\Build\vcvarsall.bat"  x86  
**********************************************************************  
** Visual Studio 2017 Developer Command Prompt v15.0  
** Copyright (c) 2017 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x86'  
###  
### Using 'vc141' toolset.  
###  
  
D:\boostbuilder\builder-sources\src\engine>C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.15.26726\bin\Hostx64\x64\cl.exe /nologo /Zi /MT /TP /Feb2 /wd4996 /Ox /GL   -DNDEBUG  builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp filent.cpp filesys.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam.cpp jambase.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp regexp.cpp rules.cpp scan.cpp search.cpp jam_strings.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp /link kernel32.lib advapi32.lib user32.lib   
  
D:\boostbuilder\builder-sources\src\engine>dir *.exe   
 Volume in drive D is DATA  
 Volume Serial Number is 5A36-4724  
  
 Directory of D:\boostbuilder\builder-sources\src\engine  
  
  
D:\boostbuilder\builder-sources\src\engine>copy /b .\b2.exe .\bjam.exe   
The system cannot find the file specified.  
```  
b2 and bjam are not builded, how could I fix it or get more logs?  
Cheers

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:22:13 UTC  
> Url: https://github.com/boostorg/build/issues/490#issuecomment-850877366  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
