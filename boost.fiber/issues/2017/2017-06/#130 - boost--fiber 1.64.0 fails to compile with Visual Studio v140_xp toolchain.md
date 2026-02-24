# #130 - boost::fiber 1.64.0 fails to compile with Visual Studio v140_xp toolchain [Closed]

> Username: torbjoernk  
> Created at: 2017-06-21 05:13:13 UTC  
> Updated at: 2020-11-20 20:00:03 UTC  
> Closed at: 2017-07-12 07:17:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/130  

I'm trying to compile boost::fiber with _Visual Studio 2017_ and it's `v140_xp` toolchain (that is the _Visual Studio 2015_ toolchain for _Windows XP_ support - don't ask ...) targeting Windows XP 32bit.  
  
As it is not directly possible with `b2` to define the toolchain name for VS, one needs to set it via environment variables as described in [this MS official blog post ("Targeting from the Command Line")](https://blogs.msdn.microsoft.com/vcblog/2012/10/08/windows-xp-targeting-with-c-in-visual-studio-2012/).  
  
With the following batch file (to be run from within the _Developer Command Prompt for VS 2017_):  
```  
:: Setting additional parameters for XP toolchain  
set INCLUDE=%ProgramFiles(x86)%\Microsoft SDKs\Windows\7.1A\Include;%INCLUDE%  
set PATH=%ProgramFiles(x86)%\Microsoft SDKs\Windows\7.1A\Bin;%PATH%  
set LIB=%ProgramFiles(x86)%\Microsoft SDKs\Windows\7.1A\Lib;%LIB%  
set CL=/D_USING_V140_SDK71_;%CL%  
  
call b2.exe --layout=versioned -q variant=debug,release link=static runtime-link=shared threading=multi cxxflags=/vmg linkflags=/SUBSYSTEM:CONSOLE,5.01 toolset=msvc-14.0 stage  
```  
boost::fiber fails to compile with the following error:  
```  
compile-c-c++ ..\all_build_fiber\boost\bin.v2\libs\fiber\build\msvc-14.0\debug\link-static\threading-multi\algo\algorithm.obj  
algorithm.cpp  
D:\3rdparty\boost\1.64.0\src\boost/intrusive/detail/parent_from_member.hpp(52): error C2338: sizeof(caster) == sizeof(int)  
D:\3rdparty\boost\1.64.0\src\boost/intrusive/detail/parent_from_member.hpp(110): note: see reference to function template instantiation 'ptrdiff_t boost::intrusive::detail::offset_from_pointer_to_member<boost::fibers::context,Member>(const Member boost::fibers::context::* )' being compiled  
        with  
        [  
            Member=boost::fibers::detail::wait_functor::hook_type  
        ]  
D:\3rdparty\boost\1.64.0\src\boost/intrusive/parent_from_member.hpp(42): note: see reference to function template instantiation 'const Parent *boost::intrusive::detail::parent_from_member<boost::fibers::context,Member>(const Member *,const Member boost::fibers::context::* )' being compiled  
        with  
        [  
            Parent=boost::fibers::context,  
            Member=boost::fibers::detail::wait_functor::hook_type  
        ]  
D:\3rdparty\boost\1.64.0\src\boost/fiber/context.hpp(598): note: see reference to function template instantiation 'const Parent *boost::intrusive::get_parent_from_member<boost::fibers::context,boost::fibers::detail::wait_functor::hook_type>(const Member *,const Member boost::fibers::context::* )' being compiled  
        with  
        [  
            Parent=boost::fibers::context,  
            Member=boost::fibers::detail::wait_functor::hook_type  
        ]  
  
    call "C:\Users\TORBJO~1\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"..\all_build_fiber\boost\bin.v2\libs\fiber\build\msvc-14.0\debug\link-static\threading-multi\algo\algorithm.obj.rsp"   
  
...failed compile-c-c++ ..\all_build_fiber\boost\bin.v2\libs\fiber\build\msvc-14.0\debug\link-static\threading-multi\algo\algorithm.obj...  
```

---

## Comment 1

> Username: olk  
> Created at: 2017-06-21 05:53:04 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-309970830  

I've no idea - I don't use Windows/MSVC for development. The compiler output shows a problem with boost's intrusive list.  
  
MSVC-14.0 seams to pass the unit-tests if you look at http://www.boost.org/development/tests/develop/developer/summary.html. So I guess the problem is related to your environment (boost installation?).

---

## Comment 2

> Username: olk  
> Created at: 2017-07-12 07:17:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-314677630  

compiles on Windows/MSVC-14.0/1 as showed in the boost regression tests and reports of other testers (related to boost release)

---

## Comment 3

> Username: idontknowwhatiamdoinghelpme  
> Created at: 2018-10-18 10:51:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-430964036  

I got the same problem and error message when using Boost::Beast. Were you able to fix this issue?

---

## Comment 4

> Username: olk  
> Created at: 2018-10-18 17:07:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-431088194  

it is a problem of boost.intrusive:  
`boost/intrusive/detail/parent_from_member.hpp(52): error C2338: sizeof(caster) == sizeof(int)`

---

## Comment 5

> Username: idontknowwhatiamdoinghelpme  
> Created at: 2018-10-22 06:06:42 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-431747312  

The issue is caused by the "cxxflags=/vmg" parameter that is used for the MSVC.  
They were able to fix this issue in boost beast:   
https://github.com/boostorg/beast/issues/1270

---

## Comment 6

> Username: isanych  
> Created at: 2020-11-20 20:00:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/130#issuecomment-731379397  

Still happening with boost 1.74 and VS2019 with /vmg, fiber is the only boost component in vcpkg which failing to compile with that option
