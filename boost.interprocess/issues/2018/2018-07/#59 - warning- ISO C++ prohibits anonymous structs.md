# #59 - warning: ISO C++ prohibits anonymous structs [-Wpedantic] [Closed]

> Username: privefl  
> Created at: 2018-07-28 22:23:24 UTC  
> Updated at: 2018-10-02 05:46:28 UTC  
> Closed at: 2018-08-13 15:50:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59  

I get these warnings from https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L145 (also L153, L178 and L180).  
  
This seems to be similar to [this issue](https://github.com/boostorg/winapi/issues/62).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-08-02 23:21:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-410097641  

Which compiler?  
  
For GCC >= 6, there is a directive to disable pedantic errors in that header:  
  
#        pragma GCC diagnostic ignored "-Wpedantic"

---

## Comment 2

> Username: privefl  
> Created at: 2018-08-03 06:29:54 UTC  
> Updated at: 2018-08-03 06:30:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-410158136  

I'm not sure, please see these reports: https://cran.r-project.org/web/checks/check_results_pcadapt.html  
  
It is said that for `r-devel-windows-ix86+x86_64`, compiler `GCC 4.9.3 (i686-posix-dwarf / x86_64-posix-seh, MinGW-W64 project)` is used. Yet, it seems to be the same compiler for `r-release-windows-ix86+x86_64`, for which there is no warning.

---

## Comment 3

> Username: privefl  
> Created at: 2018-08-11 12:15:55 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412271485  

Any update on this?  
Sorry to bother you, but this is blocking me for the submission of my R package.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2018-08-12 22:04:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412375046  

Wihout access to the compiler and it's warning it's difficult to tell. Can you please try to replace line https://github.com/boostorg/interprocess/blob/develop/include/boost/interprocess/detail/win32_api.hpp#L106 from:  
  
    if (BOOST_GCC >= 60000)  
  
to  
  
    if (BOOST_GCC >= 40800)  
  
and see if this helps?

---

## Comment 5

> Username: privefl  
> Created at: 2018-08-13 14:56:48 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412547723  

Seems it removes the warning. Thanks  
Would it be possible instead of silencing warnings to name the 4 structs so that there is no warning at all (or do something like in the other issue I linked to at the beginning)?

---

## Comment 6

> Username: igaztanaga  
> Created at: 2018-08-13 15:50:01 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412566067  

It is not possible because those definitions exactly describe the implementation and ABI provided by Microsoft windows headers so it's better to maintain full compatibility. I guess Mingw provided windows.h is treating those headers as system headers. Patch applied in commit:  
  
https://github.com/boostorg/interprocess/commit/818ef814e9eb6d22cdb259600d9705149edc721f  
  
Many thanks for the report.

---

## Comment 7

> Username: privefl  
> Created at: 2018-08-13 16:19:20 UTC  
> Updated at: 2018-08-13 16:20:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412576349  

Thanks! Do you think this change will be part of Boost release 1.69?  
@eddelbuettel is asking to make sure he can change this in R package {BH}.

---

## Comment 8

> Username: igaztanaga  
> Created at: 2018-08-13 16:32:14 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-412580456  

Yes, sure. Once develop branch has cycled in several compilers I'll merge into the release branch.

---

## Comment 9

> Username: igaztanaga  
> Created at: 2018-08-20 10:35:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-414272279  

Merged in master branch:  
  
https://github.com/boostorg/interprocess/commit/162449447d92920cc1d85a90c9c10b375daae98c

---

## Comment 10

> Username: ghost  
> Created at: 2018-10-02 02:01:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426121690  

@privefl while we wait for an update of the R package {BH}, have you found an interim solution? my CRAN submission is also stuck on this.

---

## Comment 11

> Username: eddelbuettel  
> Created at: 2018-10-02 02:11:37 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426123383  

`-Wpedantic` is not an option CRAN uses. In fact, it warns against it as not portable.  So when you two say "this blocks my submission" exactly what do you mean?  
  
As of today, 152 packages on CRAN reverse-depend on BH.  According to you two that would not be possible (modulo not all 152 using IPC but you get the idea).  
  
That said, always nice to get changes into upstream. I try to update BH once or twice a year.

---

## Comment 12

> Username: ghost  
> Created at: 2018-10-02 02:43:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426128729  

> `-Wpedantic` is not an option CRAN uses. In fact, it warns against it as not portable. So when you two say "this blocks my submission" exactly what do you mean?  
  
To speak for @privefl based on a guess, it blocks the submission in the sense that our R packages pass CRAN checks up to this warning. Specifically in my case, there seems to be no problem with the package for macOS, but the package does not pass CRAN incoming checks for Windows because of this warning.

---

## Comment 13

> Username: eddelbuettel  
> Created at: 2018-10-02 02:50:17 UTC  
> Updated at: 2018-10-02 02:53:27 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426129892  

Again, winbuilder does not set `-pedantic`, despite your claims.  I just ran four builds, and several others at rhub.  The claim that "CRAN uses `-pedantic`" is false, based on my 15 years of uploading packages there.  
  
(They do use `-Wall`, and there can be pages and pages of warnings with Boost and our BH package -- sure.  But that is mostly just annoying and _not_ blocking an upload.)

---

## Comment 14

> Username: ghost  
> Created at: 2018-10-02 03:11:52 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426134000  

I'm way out of my line of expertise here so you surely know much more about this than I do, but I have submission logs from CRAN showing precisely this warning.   
  
<img width="1167" alt="screen shot 2018-10-01 at 22 04 50" src="https://user-images.githubusercontent.com/38390464/46327216-1ae1c400-c5c6-11e8-8b40-e713661a1de1.png">

---

## Comment 15

> Username: eddelbuettel  
> Created at: 2018-10-02 03:16:49 UTC  
> Updated at: 2018-10-02 03:17:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426134949  

That does explain where `-pedantic` came from.  If _you_ are setting `-pedantic` in _your_ `src/Makevars` or `src/Makevars.win` then you only have yourself to blame.  
  
(Also posting images is in poor taste.)  
  
Here is what I ran a few hours ago for package now in incoming/.  Not using BH, but using win-builder:  
  
```  
* installing *source* package 'RcppNLoptExample' ...  
** libs  
  
*** arch - i386  
d:/Compiler/gcc-4.9.3/mingw_32/bin/g++  -I"D:/RCompile/recent/R-3.5.1/include" -DNDEBUG  -I"d:/RCompile/CRANpkg/lib/3.5/Rcpp/include" -I"d:/RCompile/CRANpkg/lib/3.5/nloptr/include"   -I"d:/Compiler/gcc-4.9.3/local330/include"     -O2 -Wall  -mtune=core2 -c RcppExports.cpp -o RcppExports.o  
d:/Compiler/gcc-4.9.3/mingw_32/bin/g++  -I"D:/RCompile/recent/R-3.5.1/include" -DNDEBUG  -I"d:/RCompile/CRANpkg/lib/3.5/Rcpp/include" -I"d:/RCompile/CRANpkg/lib/3.5/nloptr/include"   -I"d:/Compiler/gcc-4.9.3/local330/include"     -O2 -Wall  -mtune=core2 -c nlopt.cpp -o nlopt.o  
d:/Compiler/gcc-4.9.3/mingw_32/bin/g++ -shared -s -static-libgcc -o RcppNLoptExample.dll tmp.def RcppExports.o nlopt.o -Ld:/Compiler/gcc-4.9.3/local330/lib/i386 -Ld:/Compiler/gcc-4.9.3/local330/lib -LD:/RCompile/recent/R-3.5.1/bin/i386 -lR  
installing to d:/RCompile/CRANguest/R-release/lib/RcppNLoptExample/libs/i386  
  
*** arch - x64  
d:/Compiler/gcc-4.9.3/mingw_64/bin/g++ -m64 -I"D:/RCompile/recent/R-3.5.1/include" -DNDEBUG  -I"d:/RCompile/CRANpkg/lib/3.5/Rcpp/include" -I"d:/RCompile/CRANpkg/lib/3.5/nloptr/include"   -I"d:/Compiler/gcc-4.9.3/local330/include"     -O2 -Wall  -mtune=core2 -c RcppExports.cpp -o RcppExports.o  
d:/Compiler/gcc-4.9.3/mingw_64/bin/g++ -m64 -I"D:/RCompile/recent/R-3.5.1/include" -DNDEBUG  -I"d:/RCompile/CRANpkg/lib/3.5/Rcpp/include" -I"d:/RCompile/CRANpkg/lib/3.5/nloptr/include"   -I"d:/Compiler/gcc-4.9.3/local330/include"     -O2 -Wall  -mtune=core2 -c nlopt.cpp -o nlopt.o  
d:/Compiler/gcc-4.9.3/mingw_64/bin/g++ -m64 -shared -s -static-libgcc -o RcppNLoptExample.dll tmp.def RcppExports.o nlopt.o -Ld:/Compiler/gcc-4.9.3/local330/lib/x64 -Ld:/Compiler/gcc-4.9.3/local330/lib -LD:/RCompile/recent/R-3.5.1/bin/x64 -lR  
installing to d:/RCompile/CRANguest/R-release/lib/RcppNLoptExample/libs/x64  
** R  
** inst  
** byte-compile and prepare package for lazy loading  
** help  
*** installing help indices  
** building package indices  
** testing if installed package can be loaded  
*** arch - i386  
*** arch - x64  
* MD5 sums  
packaged installation of 'RcppNLoptExample' as RcppNLoptExample_0.0.1.zip  
* DONE (RcppNLoptExample)  
In R CMD INSTALL  
```  
  
No `-pedantic`.  Besides the two sets of `-I...` from the `LinkingTo` I set, we have   
    `-O2 -Wall  -mtune=core2 `   
and nothing else.    
  
(And out want to look at `00install.out`, not `00check.log`.)

---

## Comment 16

> Username: eddelbuettel  
> Created at: 2018-10-02 03:18:36 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426135314  

In any event, we are abusing the poor old Boost Interprocess issue ticket here.   Ask you r-package-devel if you have questions, we're there to help.

---

## Comment 17

> Username: privefl  
> Created at: 2018-10-02 05:46:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/59#issuecomment-426156101  

You need to "answer all" to the mail your receive from CRAN and explain why you think that this warning is a false positive.  
  
I said:  
> Package BH comes with these issues on 'r-devel-windows-ix86+x86_64' only. This is also the case for many other packages (e.g. package bigmemory) and should be fixed in the next version of package BH.  
  
Uwe Ligges was OK with this explanation. And my package is now on CRAN, despite this warning.
