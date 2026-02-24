# #316 - VS 2019: thread_specific_ptr causes unbalanced destructor calls of hold objects [Closed]

> Username: Dani-Hub  
> Created at: 2020-04-18 17:57:46 UTC  
> Updated at: 2020-07-03 12:55:56 UTC  
> Closed at: 2020-07-03 12:55:56 UTC  
> Url: https://github.com/boostorg/thread/issues/316  

We are using Boost since years in our company with various compilers, among them the Visual Studio compiler family. We are currently considering to use VS 2019 in production code and I'm evaluating the most recent version with the most recent Boost release version 1.72 (I also tested 1.70 which we currently use in production code, and there is no difference for the here discussed issue between both boost versions). Our currently production-use compiler is VS 2017 and the here described issue does not exist. In our code base we have a very simple test case that verifies that the following remark in [https://www.boost.org/doc/libs/1_72_0/doc/html/thread/thread_local_storage.html](https://www.boost.org/doc/libs/1_72_0/doc/html/thread/thread_local_storage.html):  
  
> Note: on some platforms, cleanup of thread-specific data is not performed for   
> threads created with the platform's native API. On those platforms such cleanup   
> is only done for threads that are started with boost::thread unless   
> boost::on_thread_exit() is called manually from that thread.  
  
Our assumption is that on windows and on the linux versions that we support there is no need for calling `boost::on_thread_exit()` manually in threads _not_ started by `boost::thread`, therefore our minimalistic test to validate this, see the following attachment where I have removed all parts that are not related to Windows:  
  
[test_tss_direct.cpp.txt](https://github.com/boostorg/thread/files/4497139/test_tss_direct.cpp.txt)  
  
This test succeeds for VS 2012 and VS 2017, but starts failing with VS 2019: Here the test output is:  
  
`[doTestBoostThreadSpecificPtrCleanup] TSS instance counter is different from 1: 2 `  
  
and the program returns 1 instead of 0. In other words: Only one destructor of the two created `InstanceCountingClass` objects is called.   
  
_The problem could only be observed for Release builds, but for both 32 and 64 bit_.  
  
For this isolated test I build the required boost libraries using this command (The following restricts for the 32-bit case):  
  
`b2 -j%NUMBER_OF_PROCESSORS% toolset=msvc-14.2 --with-thread --with-atomic address-model=32 link=static,shared variant=release optimization=speed cxxflags="/Zc:inline" --stagedir=%BOOST_HOME%\boost_install\1_72\vc142-test\x86 --compiler="C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.25.28610\bin\HostX64\x64\cl.exe"`  
  
using the most recent VS 2019 community edition (16.5.4). I created an empty project without changing any of the default compiler settings accept that I added to the Additional Include directories the value "$(BOOST_HOME)\boost_1_72_0;" and to the Additional Linker Directories the value "$(BOOST_HOME)\boost_install\1_72\vc142-test\$(PlatformTarget)\lib;"  
  
This is the list of all effective compiler options as seen from the Command Line All options view:  
  
`/permissive- /GS /GL /analyze- /W3 /Gy /Zc:wchar_t /I"C:\Projects\ThirdParty\boost_1_72_0" /Zi /Gm- /O2 /sdl /Fd"Release\vc142.pdb" /Zc:inline /fp:precise /D "NDEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /Gd /Oy- /Oi /MD /FC /Fa"Release\" /EHsc /nologo /Fo"Release\" /Fp"Release\tss-direct-test.pch" /diagnostics:column `  
  
I should point out that we observed a similar test failure around 2017 with an older boost version but it vanished (reliably) at some point where I either updated the boost version or got a compiler update of VS 2017. Unfortunately we never understood which of these changes had the healing effect in the past, but we were happy and kept this test and run basically once in the week without any test failure since 2017 - up to the point were I started evaluating the VS 2019 compiler. It is completely possible that this is a compiler bug, but before I report it to Microsoft I would like to ask you to have a look at it, because we use all boost libraries (including the thread library) as static libraries and AFAIK the code in `tss_pe.cpp` may be very sensitive to changes in intrinsics.  
  
Thanks for your support in advance!  
  
P.S.: Today I realized that while I made a bunch of experiments to reduce the boost build parameter set to a minimal one I forgot in one of my last tests to delete the bin.v2 directory where cached build results where present. I checked again and found that the issue is reproducible, when the /Zc:inline compiler flag is active _while building boost-thread_ (but not when using /Zc:inline in the depending code). I corrected therefore the reproducer build parameter list above.  
  
Question: Is it possible to perform a complete boost build using the b2 commands but handling *one* library (In this case Boost Thread) special?

---

## Comment 1

> Username: Dani-Hub  
> Created at: 2020-04-23 16:05:56 UTC  
> Url: https://github.com/boostorg/thread/issues/316#issuecomment-618488092  

I have opened now a [Visual Studio issue](https://developercommunity.visualstudio.com/content/problem/999092/vs-2019-boost-thread-with-zcinline-causes-thread-s.html) and keep you informed about the progress.

---

## Comment 2

> Username: Dani-Hub  
> Created at: 2020-04-28 16:06:49 UTC  
> Updated at: 2020-04-28 16:07:14 UTC  
> Url: https://github.com/boostorg/thread/issues/316#issuecomment-620701870  

> I have opened now a [Visual Studio issue](https://developercommunity.visualstudio.com/content/problem/999092/vs-2019-boost-thread-with-zcinline-causes-thread-s.html) and keep you informed about the progress.  
  
The Microsoft developers have looked into this, but they have a hard time understanding the boost code of thread_specific_ptr. Since I don't know whether you are able to read their response, here it is:  
  
>Hey I am reading through this bug description, and it's not clear to me what the code generation >issue is, exactly. It seems like boost isn't operating properly in the presence of Zc:inline, but there is >a significant gap in understanding here as to how that translates into generated code (and where >exactly the issue is).  
>  
> Have you tried filing an issue with the boost team? I think to make forward progress here might >depend a lot on knowledge of the implementation of boost_thread_ptr and a clear understanding >of how that is impacted by Zc:thread. Given what Zc:inline does, this might be a boost bug where >they have the incorrect linkage type on one or more classes/variables.  
  
It would be great, if someone could take a look and try to help here - Thanks!

---

## Comment 3

> Username: Dani-Hub  
> Created at: 2020-04-28 16:49:44 UTC  
> Url: https://github.com/boostorg/thread/issues/316#issuecomment-620727570  

The Boost 1.73 release report didn't mention any changes in Boost Thread, but nonetheless I tested the new version: Unfortunately, the problem still exist.

---

## Comment 4

> Username: Kojoley  
> Created at: 2020-06-14 17:20:01 UTC  
> Url: https://github.com/boostorg/thread/issues/316#issuecomment-643795968  

I have opened a PR with a fix.  
  
The issue is reproducible with `b2 variant=release toolset=msvc,clang-win link=static libs/thread/test//tss_test` (I omitted `cxxflags=/Zc:inline` because B2 passes it by default on develop branch already).

---

## Comment 5

> Username: Dani-Hub  
> Created at: 2020-06-14 17:38:05 UTC  
> Url: https://github.com/boostorg/thread/issues/316#issuecomment-643798017  

Thanks very much, Kojoley, for your work on this!
