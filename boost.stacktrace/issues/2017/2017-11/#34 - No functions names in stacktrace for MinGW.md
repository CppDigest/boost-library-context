# #34 - No functions names in stacktrace for MinGW [Closed]

> Username: githubuser0xFFFF  
> Created at: 2017-11-03 07:36:15 UTC  
> Updated at: 2018-12-22 16:40:28 UTC  
> Closed at: 2017-11-24 20:26:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34  

Hi,  
  
we use the MinGW toolchain that ships with the Qt Libraries. This is the output of gcc -v  
  
```  
Thread model: posix  
gcc version 5.3.0 (i686-posix-dwarf-rev0, Built by MinGW-W64 project)  
```  
  
The stacktrace does not print the functions names. This is the test programm  
  
```c++  
int functionA()  
{  
	std::cout << boost::stacktrace::stacktrace() << std::endl;  
	return 0;  
}  
  
int functionB()  
{  
	return functionA();  
}  
  
int main()  
{  
	functionB();  
	return 0;  
}  
```  
This is the output using **BOOST_STACKTRACE_USE_WINDBG**:  
  
```  
 0# 0x00401B08 in EventTest  
 1# 0x00401B66 in EventTest  
 2# 0x00401B78 in EventTest  
 3# 0x004013E2 in EventTest  
```  
  
This is the output using **BOOST_STACKTRACE_USE_BACKTRACE**:  
  
```  
 0# 0x00401A40 in C:\projects_neon\EventTest\Debug\EventTest.exe  
 1# 0x00401A9E in C:\projects_neon\EventTest\Debug\EventTest.exe  
 2# 0x00401AB0 in C:\projects_neon\EventTest\Debug\EventTest.exe  
 3# 0x004013E2 in C:\projects_neon\EventTest\Debug\EventTest.exe  
```  
  
So no functions names are printed.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-11-04 20:15:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-341926681  

This is a dup for https://github.com/boostorg/stacktrace/issues/14. Note that you may help with making MinGW better by voting for the MinGW issue from this comment https://github.com/boostorg/stacktrace/issues/14#issuecomment-336555169. The upcoming Boost 1.66 release will have the fixes for #14 and you will be able to get function names on MinGW with **BOOST_STACKTRACE_USE_BACKTRACE**. Here are some [MinGW docs](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html#stacktrace.configuration_and_build.mingw_specific_notes)

---

## Comment 2

> Username: githubuser0xFFFF  
> Created at: 2017-11-05 18:46:50 UTC  
> Updated at: 2017-11-05 18:47:21 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-341995335  

Sorry, I had a typo in the initial issue report - I just fixed it. I tried both, **BOOST_STACKTRACE_USE_WINDBG** and **BOOST_STACKTRACE_USE_BACKTRACE** and both are not working. Neither of these variants outputs the function names. I have properly built and linked **libbacktrace** but I do not get any function names printed (see the output given above).  
  
So what exactly do I have to do to get proper function names with **BOOST_STACKTRACE_USE_WINDBG** and / or what do I have to do to get proper functions names from **BOOST_STACKTRACE_USE_BACKTRACE**. The [documentation](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html#stacktrace.configuration_and_build.mingw_specific_notes) states:  
  
> MinGW-w64 users may use the BOOST_STACKTRACE_USE_WINDBG and BOOST_STACKTRACE_USE_WINDBG_CACHED for getting the best results.  
  
For me the best result would mean a stacktrace with readable function names.   
  
Btw, I upvoted the MinWG issue on soureforge.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-11-06 10:57:59 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-342115057  

Could you please give a link to your download of MinGW. I'll download it and debug this issue and #36  locally.

---

## Comment 4

> Username: githubuser0xFFFF  
> Created at: 2017-11-06 11:38:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-342123946  

I use the MinGW 5.3 that ships with Qt. You can download the online installer here:  
  
[http://download.qt.io/official_releases/online_installers/qt-unified-windows-x86-online.exe](http://download.qt.io/official_releases/online_installers/qt-unified-windows-x86-online.exe)  
  
In the online installer you can select the components to install. Select **Qt -> Tools -> MinGW 5.3.0**.

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-11-12 19:38:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-343761442  

OK, I've reproduced the issue locally with Boost 1.65.1  
On current master (and on upcoming Boost 1.66) everything works with BOOST_STACKTRACE_USE_BACKTRACE.  
  
BOOST_STACKTRACE_USE_WINDBG works sometimes, depending on the linkage and symbol location. For example if I link the library dynamically and symbol is located in a dynamic library, then the function names appear.  
  
BOOST_STACKTRACE_USE_WINDBG_CACHED may work or not, depending on the thread_local support. thread_local depends on MinGW build flags. I've just removed that info from docs for briefety and because BOOST_STACKTRACE_USE_BACKTRACE is a superior solution.  
  
See updated [docs here](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html)

---

## Comment 6

> Username: apolukhin  
> Created at: 2017-11-24 20:26:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-346892339  

Just merged the updated docs to master

---

## Comment 7

> Username: pabitrad  
> Created at: 2018-12-22 16:40:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/34#issuecomment-449582551  

BOOST_STACKTRACE_USE_BACKTRACE does not work for me in Visual studio 2017. What could be the reason?
