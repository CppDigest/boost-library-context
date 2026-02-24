# #53 - Fails to compile on NVCC CUDA 11.0 [Closed]

> Username: ghost  
> Created at: 2021-02-04 13:06:40 UTC  
> Updated at: 2021-02-12 14:41:34 UTC  
> Closed at: 2021-02-12 14:41:34 UTC  
> Url: https://github.com/boostorg/signals2/issues/53  

The following code snippet will not compile in Nvidia's compiler.  
  
```  
  #include <boost/signals2/signal.hpp>  
    
  // TODO: This is an example of a library function  
  void fnBoostPreprocessorHeader()  
  {  
  }  
  
```  
The compiler chokes somewhere inside of the include statement without any additional code except the snippet above. The following error is returned:  
  
C1012 unmatched parenthesis: missing ')' BoostPreprocessorHeader in C:\vcpkg\installed\x64-windows-static\include\boost\preprocessor\slot\detail\shared.hpp on line 27  
  
I have spoken to the nvidia compiler group and they responded with the following:  
  
```  
We checked the issue and confirmed that it is a Boost issue .The error is emitted by Microsoft cl.exe compiler. Something in the    Boost headers is checking for one of the CUDA macros (__CUDACC__ / __NVCC__) and enabling code that is not accepted by the Microsoft compiler used for preprocessing.  
We suggest you track a ticket to Boost with this reproducer . Thank you.  
  
Nvidia  
```

---

## Comment 1

> Username: fmhess  
> Created at: 2021-02-05 17:08:44 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-774163360  

Would you try adding   
  
#define BOOST_PP_VARIADICS 0  
  
at the top of your file before the include?  And also try setting it to 1?  I ask this because Boost.Preprocessor also has a check for CUDA in it, related to enabling variadic support.

---

## Comment 2

> Username: ghost  
> Created at: 2021-02-09 19:14:06 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776175112  

@fmhess Neither variation of your suggestion worked. Any other work around suggestions?   
  
![image](https://user-images.githubusercontent.com/35534426/107415116-591e1500-6ae9-11eb-9702-d0af4230f7a8.png)  
![image](https://user-images.githubusercontent.com/35534426/107415140-60452300-6ae9-11eb-9944-b56423d136f2.png)

---

## Comment 3

> Username: fmhess  
> Created at: 2021-02-09 21:39:38 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776261898  

On Tue, Feb 9, 2021 at 11:14 AM dahubley <notifications@github.com> wrote:  
>  
> @fmhess Neither variation of your suggestion worked. Any other work around suggestions?  
>  
  
Well, I do development on Linux but I do have a windows machine.  Is  
it possible for me to download a free nvcc/msvc environment so I can  
reproduce the problem?  Boost doesn't seem to have any nvcc setups in  
their array of test machines.  
  
From your error message, it seems BOOST_PP_VALUE is getting set to  
something strange in boost/preprocessor/slot/detail/shared.hpp .  So  
you could try narrowing this down to a Boost.Preprocessor issue, it  
must be happening during the expansion of one of the BOOST_PP macros  
used in a signals2 header.  
  
--   
Frank

---

## Comment 4

> Username: ghost  
> Created at: 2021-02-09 21:56:20 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776270206  

@fmhess I below is a link to the NVCC compiler.   
[NVCC Download](https://developer.nvidia.com/cuda-downloads)  
  
You can install the visual studio plugin to make the set up easier.  
  
Would it be possible to get boost to start compiling against NVCC? Heterogeneous computing (i.e. GPGPU) is growing in popularity in various fields where C++ is the dominate language. Also hiding dependencies can be a very laborious process for zero gain.   
  
Thanks for your help!  
Drew

---

## Comment 5

> Username: fmhess  
> Created at: 2021-02-09 23:41:55 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776320633  

I'll try out nvcc tomorrow.  I just maintain Signals2, so you should  
probably take your more general concerns to the boost-devel mailing list.  
You could volunteer to run the boost regression tests on your platform (see  
https://www.boost.org/development/testing.html ) then all the library  
maintainers would be able to see if their tests pass on nvcc on windows.  
  
Actually, something else you could try now would be to run the regression  
tests for Boost.Preprocessor and see if they pass.  
  
On Tue, Feb 9, 2021, 16:56 dahubley <notifications@github.com> wrote:  
  
> @fmhess <https://github.com/fmhess> I below is a link to the NVCC  
> compiler.  
> NVCC Download <https://developer.nvidia.com/cuda-downloads>  
>  
> You can install the visual studio plugin to make the set up easier.  
>  
> Would it be possible to get boost to start compiling against NVCC?  
> Heterogeneous computing (i.e. GPGPU) is growing in popularity in various  
> fields where C++ is the dominate language. Also hiding dependencies can be  
> a very laborious process for zero gain.  
>  
> Thanks for your help!  
> Drew  
>  
> —  
> You are receiving this because you were mentioned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/signals2/issues/53#issuecomment-776270206>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AARKTXYGAOMBY64BRBKZIN3S6GVRJANCNFSM4XCZNEIA>  
> .  
>

---

## Comment 6

> Username: eldiener  
> Created at: 2021-02-10 18:45:21 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776928354  

Boost preprocessor now assumes variadic macro support so BOOST_PP_VARIADICS is always set to 1.

---

## Comment 7

> Username: ghost  
> Created at: 2021-02-10 18:50:30 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-776931433  

@eldiener is this a recent change?

---

## Comment 8

> Username: fmhess  
> Created at: 2021-02-11 03:55:38 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777188266  

I installed the latest nvcc 11.2, visual studio community 2019, and boost-1.75 and I couldn't reproduce your problem:  
```  
PS C:\Users\fhess\source\repos\CudaRuntime1\CudaRuntime1> nvcc -I C:\Users\fhess\Downloads\boost_1_75_0\boost_1_75_0\ -c .\Source.cpp  
Source.cpp  
PS C:\Users\fhess\source\repos\CudaRuntime1\CudaRuntime1> ls  
  
  
    Directory: C:\Users\fhess\source\repos\CudaRuntime1\CudaRuntime1  
  
  
Mode                LastWriteTime         Length Name  
----                -------------         ------ ----  
d-----        2/11/2021   2:44 AM                x64  
-a----        2/11/2021   3:18 AM           4721 CudaRuntime1.vcxproj  
-a----        2/11/2021   2:40 AM            168 CudaRuntime1.vcxproj.user  
------        2/11/2021   2:40 AM           3819 kernel.cu  
-a----        2/11/2021   3:22 AM            131 Source.cpp  
-a----        2/11/2021   3:23 AM           2140 Source.obj  
  
  
PS C:\Users\fhess\source\repos\CudaRuntime1\CudaRuntime1> nvcc --version  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2021 NVIDIA Corporation  
Built on Thu_Jan_28_19:41:49_Pacific_Standard_Time_2021  
Cuda compilation tools, release 11.2, V11.2.142  
Build cuda_11.2.r11.2/compiler.29558016_0  
```  
  
Can you try nvcc 11.2 and boost-1.75 and see if your problem still exists?

---

## Comment 9

> Username: ghost  
> Created at: 2021-02-11 14:51:12 UTC  
> Updated at: 2021-02-11 14:53:58 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777520573  

Unfortunately we are no closer to finding the issue. I ran the compiler on the file from command line. The result was the same. I have added to cl.exe verison, CUDA verison, and compiler output for you.  
  
```  
PS C:\Users\Drew Hubley\source\repos\BoostPreprocessorHeader\BoostPreprocessorHeader> nvcc -I "C:\Users\Drew Hubley\Downloads\boost_1_75_0\boost_1_75_0" -c func.cu  
func.cu  
C:/Users/Drew Hubley/Downloads/boost_1_75_0/boost_1_75_0\boost/preprocessor/slot/detail/shared.hpp(27): fatal error C1012: unmatched parenthesis: missing ')'  
PS C:\Users\Drew Hubley\source\repos\BoostPreprocessorHeader\BoostPreprocessorHeader> nvcc --version  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2020 NVIDIA Corporation  
Built on Mon_Nov_30_19:15:10_Pacific_Standard_Time_2020  
Cuda compilation tools, release 11.2, V11.2.67  
Build cuda_11.2.r11.2/compiler.29373293_0  
PS C:\Users\Drew Hubley\source\repos\BoostPreprocessorHeader\BoostPreprocessorHeader> cl.exe --version  
Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29111 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
cl : Command line warning D9002 : ignoring unknown option '--version'  
cl : Command line error D8003 : missing source filename  
PS C:\Users\Drew Hubley\source\repos\BoostPreprocessorHeader\BoostPreprocessorHeader> cl.exe  
Microsoft (R) C/C++ Optimizing Compiler Version 19.27.29111 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
PS C:\Users\Drew Hubley\source\repos\BoostPreprocessorHeader\BoostPreprocessorHeader>  
```

---

## Comment 10

> Username: fmhess  
> Created at: 2021-02-11 18:30:41 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777698816  

Ok, I can reproduce the problem if I change the source code file  
extension to ".cu".  What I see is BOOST_PP_VALUE is set to  
  
BOOST_PP_TUPLE_ELEM_O_1(BOOST_PP_TUPLE_ELEM_O_1(2, 0,  
(3,(0,10,<boost/function/detail/function_iterate.hpp>))), 0,  
BOOST_PP_TUPLE_ELEM_O_1(2, 1,  
(3,(0,10,<boost/function/detail/function_iterate.hpp>))))  
  
after preprocessing.  When compiling a ".cpp" file, BOOST_PP_VALUE  
evaluates to 0.  So it looks like the BOOST_PP_TUPLE_ELEM_0_1 macro is  
failing to evaluate, it must be undefined when compiling a cuda file?

---

## Comment 11

> Username: ghost  
> Created at: 2021-02-11 19:05:27 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777719182  

The CUDA compiler will default to the MSVC compiler when asked to compile a cpp file. I think the opening message with the quote from Nvidia might be a clue to what is happening. Is there anything NVCC specific in the macros?

---

## Comment 12

> Username: eldiener  
> Created at: 2021-02-11 19:54:17 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777749646  

There is no macro called `BOOST_PP_TUPLE_ELEM_0_1` or `BOOST_PP_TUPLE_ELEM_O_1` in the preprocessor library.

---

## Comment 13

> Username: fmhess  
> Created at: 2021-02-11 19:58:50 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777752162  

The problem can be generated by preprocessing the following code with nvcc  
(in a file with the ".cu" extension.  The code is adapted from  
boost/function.hpp  
  
```  
#include <boost/preprocessor/iterate.hpp>  
  
#  define BOOST_PP_ITERATION_PARAMS_1  
(3,(0,10,<boost/function/detail/function_iterate.hpp>))  
#  include BOOST_PP_ITERATE()  
#  undef BOOST_PP_ITERATION_PARAMS_1  
#endif  
```  
  
--   
Frank

---

## Comment 14

> Username: eldiener  
> Created at: 2021-02-11 20:03:54 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777754806  

Which version of nvcc and what is the nvcc command line ?

---

## Comment 15

> Username: fmhess  
> Created at: 2021-02-11 20:11:00 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777758581  

```  
nvcc --version  
nvcc: NVIDIA (R) Cuda compiler driver  
Copyright (c) 2005-2021 NVIDIA Corporation  
Built on Thu_Jan_28_19:41:49_Pacific_Standard_Time_2021  
Cuda compilation tools, release 11.2, V11.2.142  
Build cuda_11.2.r11.2/compiler.29558016_0  
```  
```  
nvcc -I C:\Users\fhess\Downloads\boost_1_75_0\boost_1_75_0 -E .\File.cu > myout.cpp  
File.cu  
C:/Users/fhess/Downloads/boost_1_75_0/boost_1_75_0\boost/preprocessor/slot/detail/shared.hpp(40): fatal error C1012: unmatched parenthesis: missing ')'  
```  
And here is an unmangled and corrected version of the contents of the File.cu test program  
```  
#include <boost/preprocessor/iterate.hpp>  
  
#  define BOOST_PP_ITERATION_PARAMS_1 (3,(0,10,<boost/function/detail/function_iterate.hpp>))  
#  include BOOST_PP_ITERATE()  
#  undef BOOST_PP_ITERATION_PARAMS_1  
  
```

---

## Comment 16

> Username: eldiener  
> Created at: 2021-02-11 23:35:22 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777866811  

The example runs fine with all compilers I have tested. Unfortunately there is no toolset for nvcc in bjam. This suggests some sort of bug in nvcc, most likely in its preprocessor. Maybe nvcc is emulating VC++ and VC++ has numerous workarounds to make its default non-standard preprocessor work with the preprocessor library. I could change the preprocessor configuration file for nvcc so that it acts like VC++. But what you are telling me is that nvcc when compiling a .cpp file works fine but when compiling a .cu it does not work fine, so this suggests something else. Maybe nvcc only emulates VC++ when compiling a .cu file, otherwise it invokes its own compiler.

---

## Comment 17

> Username: fmhess  
> Created at: 2021-02-12 00:40:12 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777891269  

nvcc only defines `__CUDACC__` when compiling .cu files (see https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html ).  There are various places in Boost.Config and one place in Boost.Preprocessor where it checks for `__CUDACC__`.  Also this bit from include/boost/config/compiler/nvcc.hpp might be relevant:  
  
```  
//  
// When compiing .cu files, there's a bunch of stuff that doesn't work with msvc:  
//  
#if defined(_MSC_VER)  
#  define BOOST_NO_CXX14_DIGIT_SEPARATORS  
#  define BOOST_NO_CXX11_UNICODE_LITERALS  
#endif  
```

---

## Comment 18

> Username: ghost  
> Created at: 2021-02-12 02:23:15 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777926148  

NVCC complies CPP files using the VC++ compiler. Thus, is why it works,  
boost is tested again the underlying compiler in that case. NVCC uses it  
own toolchain only when compiling .cu files or using the -x option.  
  
On Thu., Feb. 11, 2021, 8:40 p.m. Frank Mori Hess, <notifications@github.com>  
wrote:  
  
> nvcc only defines __CUDACC__ when compiling .cu files (see  
> https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html ).  
> There are various places in Boost.Config and one place in  
> Boost.Preprocessor where it checks for __CUDACC__. Also this bit from  
> include/boost/config/compiler/nvcc.hpp might be relevant:  
>  
> //  
> // When compiing .cu files, there's a bunch of stuff that doesn't work with msvc:  
> //  
> #if defined(_MSC_VER)  
> #  define BOOST_NO_CXX14_DIGIT_SEPARATORS  
> #  define BOOST_NO_CXX11_UNICODE_LITERALS  
> #endif  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/signals2/issues/53#issuecomment-777891269>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AIPDMWQOK6F76HVJW4SPAI3S6R2HVANCNFSM4XCZNEIA>  
> .  
>

---

## Comment 19

> Username: ghost  
> Created at: 2021-02-12 02:58:07 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777937326  

Also, it seems like really poor practice to add compiler switches to code  
that is not testing against that toolchain. Thoughts?  
  
On Thu., Feb. 11, 2021, 8:40 p.m. Frank Mori Hess, <notifications@github.com>  
wrote:  
  
> nvcc only defines __CUDACC__ when compiling .cu files (see  
> https://docs.nvidia.com/cuda/cuda-compiler-driver-nvcc/index.html ).  
> There are various places in Boost.Config and one place in  
> Boost.Preprocessor where it checks for __CUDACC__. Also this bit from  
> include/boost/config/compiler/nvcc.hpp might be relevant:  
>  
> //  
> // When compiing .cu files, there's a bunch of stuff that doesn't work with msvc:  
> //  
> #if defined(_MSC_VER)  
> #  define BOOST_NO_CXX14_DIGIT_SEPARATORS  
> #  define BOOST_NO_CXX11_UNICODE_LITERALS  
> #endif  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/signals2/issues/53#issuecomment-777891269>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AIPDMWQOK6F76HVJW4SPAI3S6R2HVANCNFSM4XCZNEIA>  
> .  
>

---

## Comment 20

> Username: fmhess  
> Created at: 2021-02-12 03:35:53 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777947557  

If I add a `#define __CUDACC__` before the boost include I can induce the problem to happen even when the file has a ".cpp" extension.  Alternatively, if I do a `#undef __CUDACC__` before the boost include I can make the problem go away even when using a ".cu" extension.  So the problem is triggered by the boost headers changing behavior based on a check for the `__CUDACC__` define.

---

## Comment 21

> Username: fmhess  
> Created at: 2021-02-12 03:41:41 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777948945  

If I delete the check for `(defined __NVCC__ && defined __CUDACC__)` in boost/preprocessor/config/config.hpp the problem goes away.

---

## Comment 22

> Username: eldiener  
> Created at: 2021-02-12 04:34:41 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777963643  

That test was actually saying that if the VC++ default preprocessor is being used but nvcc is compiling cuda, don't use any of the many workarounds needed to deal with VC++ default's preprocessor and variadic macros. So what you are saying is that when nvcc is compiling in cuda mode, the preprocessor needs to treat it as VC++'s default preprocessor as far as dealing with variadic macro support is concerned. I am good with that change and will make it.

---

## Comment 23

> Username: eldiener  
> Created at: 2021-02-12 04:39:44 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-777965301  

I have made the change on the preprocessor 'develop' branch.

---

## Comment 24

> Username: ghost  
> Created at: 2021-02-12 14:41:34 UTC  
> Url: https://github.com/boostorg/signals2/issues/53#issuecomment-778235307  

Successfully accomplished a build of my entire project after applying the specified fix.
