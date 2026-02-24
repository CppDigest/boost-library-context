# #236 - Bootstrap.bat not working on 1.65.0+ [Closed]

> Username: killerwife  
> Created at: 2017-09-11 11:29:35 UTC  
> Updated at: 2023-12-06 07:24:31 UTC  
> Closed at: 2018-01-23 03:55:09 UTC  
> Url: https://github.com/boostorg/build/issues/236  

What I did:  
Installation instruction for minimal build of boost specify this:  
bootstrap  
.\b2  
  
What I got:  
I ran bootstrap.bat on windows 10 with visual studio 2017 installed and this is what I received in bootstrap.log:  
**********************************************************************  
** Visual Studio 2017 Developer Command Prompt v15.0.26730.12  
** Copyright (c) 2017 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x86'  
###  
### Using 'vc141' toolset.  
###  
  
C:\Users\killerwife\source>if exist bootstrap rd /S /Q bootstrap   
  
C:\Users\killerwife\source>md bootstrap   
  
C:\Users\killerwife\source>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\yyacc0 yyacc.c   
yyacc.c  
c1: fatal error C1083: Cannot open source file: 'yyacc.c': No such file or directory  
  
What is supposed to happen?  
It is supposed to go through and generate b2 for successful build.  
  
Tested with releases:  
1.64.0, 1.65.0, 1.65.1

---

## Comment 1

> Username: killerwife  
> Created at: 2017-09-11 11:50:21 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-328505649  

Update:  
By removing vc141 in guess_toolset.bat and compiling bjam using vc14, I managed to get vc141 compilation running. It seems only the bjam compilation is broken due to some new vc141 things.

---

## Comment 2

> Username: BurningEnlightenment  
> Created at: 2017-09-16 11:22:09 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-329962581  

@killerwife Have you tried to execute `bootstrap.bat` from within the vs2017 native tools command prompt?

---

## Comment 3

> Username: killerwife  
> Created at: 2017-09-16 11:46:55 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-329963724  

@BurningEnlightenment I have not. I executed it only from powershell and cmd.exe

---

## Comment 4

> Username: BurningEnlightenment  
> Created at: 2017-09-16 11:56:03 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-329964126  

I can reproduce this failure with powershell and a "plain" cmd, but using the native tools command prompt does work for me, so I'm wondering whether it works for you, too. Maybe this is intentional and just poorly documented 🤔

---

## Comment 5

> Username: SSE4  
> Created at: 2017-09-19 08:57:21 UTC  
> Updated at: 2017-09-19 08:57:48 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-330475306  

I have had a similar error, the problem is that vcvarsall.bat changes current directory to the ~/Source, e.g. in your case it is C:\Users\killerwife\source as seen in your log.  
I am setting environment variable VSCMD_START_DIR prior to executing vcvarsall.bat to bypass an error, e.g. set VSCMD_START_DIR=%CD% && vcvarsall.bat

---

## Comment 6

> Username: PayasR  
> Created at: 2017-09-26 23:55:41 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-332368868  

Tested on boost 1_65_1, I was able to compile with the dev command prompt

---

## Comment 7

> Username: Nekto89  
> Created at: 2017-10-19 10:30:25 UTC  
> Updated at: 2017-10-19 10:52:59 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-337867624  

Have the same issue with 1.65.1 and VS2017 Update 2. Boost 1.64.0 was properly compiled in same environment without problems. This seems to be working as a workaround:  
  
[vc141_configure.patch.txt](https://github.com/boostorg/build/files/1398023/vc141_configure.patch.txt)  
  
PS I also have installed vs2010, 2012, 2013 and 2015. Probably one of them was used by default in 1.64.0 instead of VS2017 - that's why it worked.

---

## Comment 8

> Username: shleym2000  
> Created at: 2017-10-28 12:19:15 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-340186860  

Thanks, PayasR.  
  
I got an error, but then started building boost 1_65_1 from Visual Studio Development Command Prompt and it works marvelously

---

## Comment 9

> Username: DISAPPEARED13  
> Created at: 2023-12-06 06:52:48 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-1842193176  

Hi there, I tried to use vs2017 native tools command prompt to run bootstrap.bat, but some relative problem occurs to me... when compiling the cpp file, shows that `fatal error C1083, cannot open stdint.h, no such file or directory`, I found that `tools/build/src/engine/config.h` codes `#include <stdint.h>`, and I tried to compile my random project with include the stdint.h, and succeeded.   
Why run bootstrap.h causes C1083, I have these head files...  
Could anyone give me an advice!! Thanks a lot!

---

## Comment 10

> Username: DISAPPEARED13  
> Created at: 2023-12-06 07:24:30 UTC  
> Url: https://github.com/boostorg/build/issues/236#issuecomment-1842235229  

I tried to run bootstrap.bat with TOOLSET mingw, now it can run...but I have no idea is it right
