# #222 Workaround for over-sized object files on Mingw. [Merged]

> Username: jzmaddock  
> Created at: 2020-04-18 18:00:25 UTC  
> Updated at: 2020-04-21 17:24:26 UTC  
> Merged at: 2020-04-21 10:00:26 UTC  
> Closed at: 2020-04-21 10:00:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222  

Fixes https://github.com/boostorg/multiprecision/issues/219

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-04-18 18:05:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-615915489  

@pabristow : you should see the command line as:  
  
```  
 "g++"   -fvisibility-inlines-hidden -m64 -mthreads -O0 -fno-inline -Wall -fvisibility=hidden -Wa,-mbig-obj -Wno-missing-braces -DBOOST_ALL_NO_LIB=1  -I"..\..\.." -I"..\include"  -c -o "..\..\..\bin.v2\libs\multiprecision\example\floating_point_examples.test\gcc-9.2\debug\debug-symbols-off\threading-multi\visibility-hidden\floating_point_examples.o" "floating_point_examples.cpp"  
```  
  
Which works for me with MSys64 GCC-9.2, but it is right on the limit :(

---

## Comment 2

> Username: pabristow  
> Created_at: 2020-04-18 21:32:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-615953267  

I can confirm that 9.2.0 works OK, so your fix is correct, for which thanks.  
  
But GCC 10 looks like the straw that breaks the camels back :-(  
  
Is it time to break down the floating_point_examples to smaller examples?  
  
Shall I investigate?  Where does it report the fraction of table size used?

---

## Comment 3

> Username: pabristow  
> Created_at: 2020-04-20 09:04:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-616413376  

You asked about the command line, but it is now lost, and because I was on a detached HEAD, I seem not to have the log file for my failure.  Perhaps I can retry after you have merged big_obj changes to develop to make sure we are both using the same code?  What is the off option? that you added?

---

## Comment 4

> Username: eldiener  
> Created_at: 2020-04-20 19:26:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-616760286  

You can always see the command line which b2 generates to build anything by passing the -d2 switch to the b2 invocation.  
  
I am waiting for this PR to merge before testing it out with gcc on Windows. I have all recent released versions of gcc on which I can easily test, but I do not have 10.0 since it has not been officially released. I can probably build 10.0 from https://github.com/niXman/mingw-builds but I have learned that for my sanity playing around with non-released software is rarely productive for me. But occasionally I do, as in Visual Studio 2019 Preview where Microsoft has a really easy to use GUI for keeping it up to date.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2020-04-21 10:00:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-617081254  

Merging now, like @eldiener I tend to ignore pre-release gcc versions as they often have multiple issues which ultimately get fixed.

---

## Comment 6

> Username: eldiener  
> Created_at: 2020-04-21 17:24:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/222#issuecomment-617301286  

I tested mingw-w64/gcc-9.3 on Windows 10 and your fix works for the floating_point_examples. Thanks !

---
