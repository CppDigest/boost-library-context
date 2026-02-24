# #302 - I am trying to compile 1_67_0 and getting stuck at the bootstrap step [Open]

> Username: seangrogan  
> Created at: 2018-05-03 21:37:58 UTC  
> Updated at: 2021-06-26 03:10:14 UTC  
> Url: https://github.com/boostorg/build/issues/302  

hello,   
  
I know I can pick up the binaries, but I can't for all the libraries I want to work with, so I decided to practice with boost, seeing as it might be well documented.    
  
I first downloaded and extracted boost_1_67_0 to C:\Program Files\boost\boost_1_67_0.  I opened command prompt in admin mode and ran bootstrap.bat.  In the cmd window, i see "yyacc no such file or directory" and bootstrap fails to finish.    
  
I tried also doing the steps here: https://github.com/boostorg/build/issues/236  
  
and here: https://stackoverflow.com/questions/30960348/how-to-compile-boost-on-windows-10-with-visual-studio (modified for VS 14.0)  
  
All in admin mode, I tried running bootstrap in VS, PowerShell, and Command Prompt.    
  
I'm not sure what extra debugging steps to take or what pertinent information is needed.    
  
bootsrap.log:  
  
`  
`**********************************************************************`  
`** Visual Studio 2017 Developer Command Prompt v15.6.6`  
`** Copyright (c) 2017 Microsoft Corporation`  
`**********************************************************************`  
`[vcvarsall.bat] Environment initialized for: 'x86'  
`###  
`### Using 'vc141' toolset.  
`###  
  
`C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
`C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>md bootstrap   
  
`C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ `/Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\yyacc0 `yyacc.c   
`yyacc.c  
  
`C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>.\bootstrap\yyacc0 jamgram.y `jamgramtab.h jamgram.yy   
  
`C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ `/Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  `command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c `hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c `parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c `strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c `modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
`command.c  
`compile.c  
`constants.c  
`debug.c  
`execcmd.c  
`execnt.c  
`filent.c  
`frames.c  
function.c  
glob.c  
hash.c  
hdrmacro.c  
headers.c  
jam.c  
jambase.c  
jamgram.c  
lists.c  
make.c  
make1.c  
object.c  
Generating Code...  
Compiling...  
option.c  
output.c  
parse.c  
pathnt.c  
pathsys.c  
regexp.c  
rules.c  
scan.c  
search.c  
subst.c  
timestamp.c  
variable.c  
modules.c  
strings.c  
filesys.c  
builtins.c  
md5.c  
class.c  
cwd.c  
w32_getreg.c  
Generating Code...  
Compiling...  
native.c  
set.c  
path.c  
regex.c  
property-set.c  
sequence.c  
order.c  
Generating Code...  
  
C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc141 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\Tools\..\..\VC\ "  clean   
don't know how to make clean  
...found 1 target...  
...can't find 1 target...  
  
C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc141 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\Tools\..\..\VC\ "    
don't know how to make all  
...found 1 target...  
...can't find 1 target...  
  
C:\Program Files\boost\boost_1_67_0\tools\build\src\engine>exit /b 1   
  
`

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:32 UTC  
> Url: https://github.com/boostorg/build/issues/302#issuecomment-868936479  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
