# #22 - Compiling error in project slic3r with cenv.hpp in line 104 with MinGW toolchain on windows 32 bit XP/7 [Closed]

> Username: redPrint7  
> Created at: 2017-08-10 10:00:44 UTC  
> Updated at: 2017-08-10 10:10:09 UTC  
> Closed at: 2017-08-10 10:06:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/22  

The error was reported to the project, the mantainers refferd me to your project for a permanent fix:  
The original issue:  
perl Build.PL stops with the following error:  
file \xs\src\boost\nowide\cenv.hpp  
Error: cenv.hpp:104:41: error: ISO C++ forbids comparison between pointer and integer [-fpermissive]  
in cenv.hpp line 104/41  
  
if I change from  
  
while(*key_end!='=' && key_end!='\0')  
  
to  
while(*key_end!='=' && key_end!="\0") //changed '\0' to "\0"  
it works as expected.  
Infos on build environment perl 5.26 compiled from source on WinXP SP3  
Mingw-w64 v5.02 toolchain  
  
Problem could be reproduced on win 7 ultimate 32bit with the same toolchain - it just popped up on XP  
the compiler is the latest stable version.  
  
So hopefully there is an possibility to get rid of the error globaly.  
Link to the slic3r project: https://github.com/alexrj/Slic3r/  
  
Thanks in advance  
  
red007

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2017-08-10 10:06:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/22#issuecomment-321509319  

Duplicate of #14

---

## Comment 2

> Username: redPrint7  
> Created at: 2017-08-10 10:10:09 UTC  
> Url: https://github.com/boostorg/nowide/issues/22#issuecomment-321510157  

Yes I noticed issue 14 before, but here comilation stops the hard way - just to let you know this is serious trouble for some projects.  
Thanks for careing in advance
