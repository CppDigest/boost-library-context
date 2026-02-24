# #44 - boost::process::group crashes with "stack-based buffer overrun" [Closed]

> Username: petke  
> Created at: 2018-05-23 00:37:15 UTC  
> Updated at: 2019-04-10 02:15:21 UTC  
> Closed at: 2019-04-10 02:15:21 UTC  
> Url: https://github.com/boostorg/process/issues/44  

I upgraded from "boost_1_65_1_vc141" to "boost_1_67_0_vc1914" and my program started crashing in release mode. I never saw this in the old version. It took me a day or two, but I narrowed down the error to this toy example. It crashes the same.  
  
```  
#include <boost/process.hpp>  
  
int main()  
{  
	boost::process::group group_; //In Debug mode it crashes here in boost\process\detail\windows\job_workaround.hpp : query_information_job_object : "Run-Time Check Failure #0 - The value of ESP was not properly saved across a function call.  This is usually a result of calling a function declared with one calling convention with a function pointer declared with a different calling convention."  
  
} //In Release mode it crashes here: "Unhandled exception at 0x00A74ACB in crash_test.exe: Stack cookie instrumentation code detected a stack-based buffer overrun."  
  
  //settings&versions:  
  //boost version: boost_1_67_0  
  //vs version: Microsoft Visual C++ 2017, _MSC_VER 1914  
  //Default Debug mode command line settings:   "/permissive- /GS /analyze- /W3 /Zc:wchar_t /I"C:\API\boost_1_67_0_vc1914" /ZI /Gm- /Od /sdl /Fd"Debug\vc141.pdb" /Zc:inline /fp:precise /D "_SCL_SECURE_NO_WARNINGS" /D "WIN32" /D "_DEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /RTC1 /Gd /Oy- /MDd /FC /Fa"Debug\" /EHsc /nologo /Fo"Debug\" /Fp"Debug\crash_test.pch" /diagnostics:classic "  
  //Default Release mode command line settings: "/permissive- /GS /GL /analyze- /W3 /Gy /Zc:wchar_t /I"C:\API\boost_1_67_0_vc1914" /Zi /Gm- /O2 /sdl /Fd"Release\vc141.pdb" /Zc:inline /fp:precise /D "WIN32" /D "NDEBUG" /D "_CONSOLE" /D "_UNICODE" /D "UNICODE" /errorReport:prompt /WX- /Zc:forScope /Gd /Oy- /Oi /MD /FC /Fa"Release\" /EHsc /nologo /Fo"Release\" /Fp"Release\crash_test.pch" /diagnostics:classic "  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-05-23 06:55:08 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391240553  

Thanks for the report, I'm really sorry you needed to spent so much time on that.  
  
It also seems very odd, I have an active unit-test for that, so that should (in theory of course) crash. Are you compiling against x86 or x64?

---

## Comment 2

> Username: petke  
> Created at: 2018-05-23 09:53:36 UTC  
> Updated at: 2018-05-23 10:24:28 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391290264  

No, worries. Your library has been very useful to me.   
  
I'm compiling against x86, (although the machines I run on are "Windows Home 10 x64" and "Windows Server 2012 R2 x64")  
  
I just tested just to make sure. The same example program doesn't crash if i build against boost_1_65_1, but it does crash if i build against boost_1_67_0.  
  
The old boost_1_65_1 boost library was compiled on 10/10/2017. I tagged it with version vc141, so I think that means some version of "Visual Studio 2017".  
  
The new boost_1_67_0 library and program that has this problem, is compiled with "Visual Studio community 2017 update version 15.7.2".  
  
Its possibly I screwed something up when i built the new boost library. I could try it again later. In the meantime I will try a workaround (maybe I can do without boost::process::group)  
  
(I also get compile warnings about "Please define _WIN32_WINNT or _WIN32_WINDOWS appropriately". Not sure if that matters ...)

---

## Comment 3

> Username: petke  
> Created at: 2018-05-23 10:16:49 UTC  
> Updated at: 2018-05-23 10:38:02 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391296606  

One final details:  
  
The test program that crashes. I did the tests in the debugger. Running from console behaves a bit different:  
  
If you run from console the debug version shows the error popup window about "Run-Time Check Failiure #0. (So it behaves much the same as in the debugger)  
  
However in release mode from console you get no error message, so you cant tell if the program exited by unhanded exception or normally. I ruled the latter out by adding a catch(...) to main and a cout, and now i can see it exits because of unhanded exception. (Maybe this matters for your unit tests)

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2018-05-23 10:49:31 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391304496  

Can you try to use this header file instead ? https://github.com/boostorg/process/blob/900aab5d6d48f726164f6adfd3ded61294beb166/include/boost/process/detail/windows/job_workaround.hpp  
  
and if that doesn't work replace `BOOST_WINAPI_WINAPI_CC` with `WINAPI` ?

---

## Comment 5

> Username: petke  
> Created at: 2018-05-23 11:19:50 UTC  
> Updated at: 2018-05-23 19:07:18 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391311471  

Looks good. That seems to work. Thanks.  
  
I tried https://github.com/boostorg/process/blob/900aab5d6d48f726164f6adfd3ded61294beb166/include/boost/process/detail/windows/job_workaround.hpp  
  
I got a bunch of compiler errors about redefinition.  
Example:   
  
1>c:\api\boost_1_67_0_vc1914\boost\process\detail\windows\job_workaround.hpp(117): error C2370: 'boost::process::detail::windows::workaround::BOOST_WINAPI_WINAPI_CC': redefinition; different storage class  
  
I then replaced all the  BOOST_WINAPI_WINAPI_CC with WINAP in that same file. the compile errors went away and so did the crash bug.   
  
I tried this on the example program. I'll try it on the main program later (and update this comment with the results).   
  
Edit: I tested the fix with my main program, and it also works now.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2018-05-23 19:29:32 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-391468704  

Right, that was a major change and somehow broke the build. It works without that on 64-Bit, I'll try to find a solution for 32-Bit until the next release.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2018-06-18 01:52:12 UTC  
> Url: https://github.com/boostorg/process/issues/44#issuecomment-397923458  

Does that work now for you (current master)?
