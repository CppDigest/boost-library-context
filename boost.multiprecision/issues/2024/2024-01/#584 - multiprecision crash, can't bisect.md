# #584 - multiprecision crash, can't bisect [Closed]

> Username: gerharfa  
> Created at: 2024-01-07 13:24:34 UTC  
> Updated at: 2024-04-11 06:35:37 UTC  
> Closed at: 2024-04-11 06:35:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584  

Hi all,  
  
I'm trying to upgrade boost in a large project derived from impala.  
The upgrade I'm attempting is from boost 1.72.0 to boost 1.83.0.  
One of my tests crashes deterministically at this frame  
  
```  
boostorg/boost#1  0x00007f0050088eb0 in void boost::multiprecision::backends::divide_unsigned_helper<boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> >(boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>*, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, unsigned long long, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>&) ()  
boostorg/boost#2  0x00007f0050089cc6 in void boost::multiprecision::backends::divide_unsigned_helper<boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> >(boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>*, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>&) ()  
  
```  
maybe that's because of invalid inputs. I'm not sure if crashes are expected behaviour for any input here.  
Anyway I'm trying to figure out what exact commit caused this problem of mine.  
My plan was to git bisect all commits between the broken one and 1.72.0.  
  
I've followed this page https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview  
However the commands  
```  
git checkout boost-1.72.0  
git submodule update --recursive   
./bootstrap.sh --without-libraries=python --prefix="some_prefix" cxxflags="-mno-avx2 -fPIC -m64"  
./b2 visibility=global link=static runtime-link=static -sNO_BZIP2=1 --toolset=gcc-9.3.0 cxxflags="-mno-avx2 -static-libstdc++ -fPIC -m64" linkflags="-mno-avx2 -static-libstdc++ -fPIC -m64" --prefix="some_prefix" -j100 install --clean  
```  
fail like this:  
  
```  
Building Boost.Build engine with toolset gcc... tools/build/src/engine/b2  
Unicode/ICU support for Boost.Regex?... /home/linuxbrew/.linuxbrew/Cellar/icu4c/73.2  
Backing up existing Boost.Build configuration in project-config.jam.25  
Generating Boost.Build configuration in project-config.jam for gcc...  
  
Bootstrapping is done. To build, run:  
  
    ./b2  
  
To generate header files, run:  
  
    ./b2 headers  
  
To adjust configuration, edit 'project-config.jam'.  
Further information:  
  
   - Command line help:  
     ./b2 --help  
  
   - Getting started guide:  
     http://www.boost.org/more/getting_started/unix-variants.html  
  
   - Boost.Build documentation:  
     http://www.boost.org/build/  
  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:820: in class@project-attributes.set from module object(project-attributes)@7059  
error: Invalid project attribute 'common-requirements' specified for project at 'libs/url/build'  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:1151: in project from module project-rules  
libs/url/build/Jamfile:29: in modules.load from module Jamfile</local/home/gerharfa/boost/libs/url/build>  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:372: in load-jamfile from module project  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:64: in load from module project  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:89: in load-used-projects from module project  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:75: in load from module project  
/local/home/gerharfa/boost/tools/build/src/build/project.jam:142: in project.find from module project  
/local/home/gerharfa/boost/tools/build/src/build-system.jam:618: in load from module build-system  
/local/home/gerharfa/boost/tools/build/src/kernel/modules.jam:295: in import from module modules  
/local/home/gerharfa/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/local/home/gerharfa/boost/boost-build.jam:17: in module scope from module  
```  
  
I've tried not building url with --without-url, but that didn't change anything  
deleting url lead to an error with cobalt, which i also deleted,  
```  
  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes (cached)  
error: Unable to find file or target named  
error:     '/local/home/gerharfa/boost/libs/cobalt/build/../../config/checks//cxx20_hdr_concepts'  
error: referred to from project at  
error:     'libs/cobalt/build'  
  
```  
after which i got this error  
```  
  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes (cached)  
error: Cannot create link boost/property_map/parallel/vector_property_map.hpp to libs/property_map/include/boost/property_map/parallel/vector_property_map.hpp.  
error: Link previously defined to another file, libs/property_map_parallel/include/boost/property_map/parallel/vector_property_map.hpp.  
  
```  
property_map seems like a core thing. It's weird to me that this doesn't build. Is checking out old commits and building them supported?  
Any help on bisecting boost or debugging this crash would be appreciated.  
  
Best regards,  
Fabian

---

## Comment 1

> Username: gerharfa  
> Created at: 2024-01-07 16:18:40 UTC  
> Updated at: 2024-01-07 16:21:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880102775  

When checking out a commit you also have to make sure to delete all submodules that were not present at that commit.  
  
```  
git clean -ffd  
git submodule foreach --recursive 'git clean -ffd && git restore .'  
```  
did it for me

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-01-07 16:49:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880110324  

I do not know if this helps, but if you work independently from submodules, and just use releases, ...  
  
Then Multiprecision was always header-only. And it still is header only.  
  
A few releases ago we also went dependency-free, standalone on Multiprecision so if you get 1.84, you can include the Multiprecision headers without having _anything_ _whatsoever_ of Boost needed.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-01-07 16:50:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880110712  

If you need help tracking down the inputs or functions, we/you could step through the releases one-by-one. This kind of thing can be challenging.  
  
If you are putting many, many big number in subroutines, also be wary of potential stack overflow, but I don't know your application. So you need to check that, it would be like hundreds or thousands of big 256-bit numbers to crash the stack.

---

## Comment 4

> Username: gerharfa  
> Created at: 2024-01-07 17:25:16 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880118665  

Only updating the multiprecision library is a good idea that I didn't explore yet, as i feared confusing compatibility issues with other parts of boost.  
  
Anyway i have a bisect now running. I'll post the first commit where the crash happens here once it's done.  
Hopefully I can shed some more light on this then.

---

## Comment 5

> Username: ckormanyos  
> Created at: 2024-01-07 17:27:03 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880119034  

> have a bisect now running. I'll post the first commit where the crash happens  
  
Great thank you.  
Yes. That's the plan. Then we can isolate the cause of this phenomenon.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-01-07 19:26:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880148499  

It shouldn't crash with any input no.  
  
In order to formulate a fix, it would be useful to have a self contained test case - so two questions: can you get a "brain dump" of the contents of the cpp_Ints's which are causing the issue?  And what was the crash - stack overflow, memory access violation, or something else?  
  
I have a hunch that the cpp_int's are in an invalid (corrupted) state when the function is called, either as something that happened earlier in our code or yours - the code in that function might do some strange things (like get stuck in infinite loops) if we have a bug in there, but there's not much that would cause an actual crash.  Hope that makes sense!  
  
Thanks for the report - we appreciate that these issues are hard to track down especially when dealing with large numbers!

---

## Comment 7

> Username: gerharfa  
> Created at: 2024-01-07 23:59:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880229243  

My bisect run finished, it took a little longer as i had to manually remove some untracked+ignored files once.  
The result:  
`# first bad commit: [c8a0330d7805272f4a164ede04953418fafe8412] Update throw_exception from master`  
which i don't understand at this time.  
  
> It shouldn't crash with any input no.  
  
thanks, that helps  
  
> In order to formulate a fix, it would be useful to have a self contained test case - so two questions: can you get a "brain dump" of the contents of the cpp_Ints's which are causing the issue? And what was the crash - stack overflow, memory access violation, or something else?  
  
tomorrow i want to hook up gdb with the failing test again. hopefully i can get you the info you need.  
i don't know if i can give you a repro of the failure, everything i work with is proprietary.  
ideally i'll figure out the root cause of this and build you a synthetic repro.  
  
The crash is a SIGABRT  
  
> Cannot access memory at address 0xb00021117000d  
> Cannot access memory at address 0xb000211170005  
> Unsupported JIT protocol version 2 in descriptor (expected 1)  
> Core was generated by `/home/gerharfa/impala/be/build/latest/service/impalad --mem_limit=105872265489 -l'.  
> Program terminated with signal SIGABRT, Aborted.

---

## Comment 8

> Username: gerharfa  
> Created at: 2024-01-08 00:04:06 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1880232098  

In another run it was a SIGSEGV  
  
```  
Thread 125 "impalad" received signal SIGSEGV, Segmentation fault.  
[Switching to Thread 0x7eff8850d700 (LWP 1904)]  
0x0000000000000000 in ?? ()  
(gdb) bt  
#0  0x0000000000000000 in ?? ()  
#1  0x00007f0050088eb0 in void boost::multiprecision::backends::divide_unsigned_helper<boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> >(boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>*, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, unsigned long long, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>&) ()  
#2  0x00007f0050089cc6 in void boost::multiprecision::backends::divide_unsigned_helper<boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> >(boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>*, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void> const&, boost::multiprecision::backends::cpp_int_backend<256ul, 256ul, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, void>&) ()  
#3  0x00007f005008ed03 in impala::DecimalOperators::Divide_DecimalVal_DecimalValWrapperABIWrapper ()  
#4  0x00000000034af986 in impala::ScalarFnCall::GetDecimalVal (this=0x1dafc000, context=0x1b00d860, row=0x1e080000)  
```

---

## Comment 9

> Username: jzmaddock  
> Created at: 2024-01-08 14:09:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1881083034  

So the memory is corrupted then, would suggest that something is trampling over the cpp_int's internals?

---

## Comment 10

> Username: gerharfa  
> Created at: 2024-01-08 14:50:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1881158322  

Yes, it's possible that impala's generated code is corrupting cpp_int's internals.  
As I progress with this issue I'm getting the feeling that this might not be a boost issue.  
  
Please feel especially free to not invest any more time into helping me with this until I have more concrete information on what exactly is going wrong here.  
  
For now I know that this problem is triggered by this commit in the boost super-project.  
```  
diff --git a/libs/throw_exception b/libs/throw_exception  
index 43a57d518c..ac72b396f5 160000  
--- a/libs/throw_exception  
+++ b/libs/throw_exception  
@@ -1 +1 @@  
-Subproject commit 43a57d518cf99fc693eebedefcbaa91074674f54  
+Subproject commit ac72b396f51bd603377ebc0088fe75d6eb43ba0e  
   
```  
  
So one of these commits  
  
```  
git rev-list 43a57d518cf99fc693eebedefcbaa91074674f54..ac72b396f51bd603377ebc0088fe75d6eb43ba0e  
ac72b396f51bd603377ebc0088fe75d6eb43ba0e  
9e8a607ad948e23acf78b53a570da2dabb77f8d3  
fdf6b240f54ef2768bfb7aff99623cd872c63db0  
81e3072d040e1fa21a7dddd9daf48bae017d3224  
fe38fbc5cfb671862a93e220203cd7a16d3b50a5  
26bc9374e2e4c20e39cc5045d9b62de210977583  
a2a78f6e46bfc0300bf5b661fc4d79bec60155bb  
c58f418c2f40d67ebf2e709781059504068a686d  
6458a1de4080b6927ceee830574bb674794b56ef  
ea9bd58f8c474cb37f34809d40796aa769fa9471  
f477e3325918fb1227a33e58340bae293dc38fc2  
8a1382d6bff8566427877fc1ed05d29041ef495f  
915a1dc49b78b2803a1fa4925003e67f34e46543  
eec2255703b66d03b4b92a99c3887015bea3e08b  
970f826a752e2d7a9a531e482c5df4082c94a20e  
2522bb5617f050dff4112002bf501e16118a34cd  
dad5cb4ed377b18e7989079b19823dae1dba137d  
```  
in the throw_exception library

---

## Comment 11

> Username: jzmaddock  
> Created at: 2024-01-08 16:45:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1881450288  

That does coincide with some big changes to Boost's common exception handling support, is there any part of the application which hasn't been recompiled with the new Boost version?  The ABI of the exception handling code changes radically in that range of commits.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2024-01-08 16:54:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1881467572  

> is there any part of the application which hasn't been recompiled with the new Boost version?  
  
nod  
  
Also I'm not sure if your application can use address sanitizers (we call them more ASAN). But over the years we have found lots of bugs in which memory regions are overlapped, overrunn, etc. simply using GCC's address sanitizers. Sometimes these give you insight into the exact line(s) where things might be going haywire...

---

## Comment 13

> Username: gerharfa  
> Created at: 2024-01-11 15:54:46 UTC  
> Updated at: 2024-01-11 15:56:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1887465789  

Thanks for the help! It's appreciated.  
  
I ran the crashing test with an ASAN build. No ASAN output.  
  
The crash happens in this line  
      https://github.com/boostorg/multiprecision/blob/e584f4f35dfa6eafe9720a78d678ac2663a835b0/include/boost/multiprecision/cpp_int.hpp#L421    
        
 the asm looks like this  
   
```  
  movabs $0x0,%r11                     │  
  mov    %rdi,0x1a8(%rsp)              │  
  mov    %r9,%rdi                      │  
  mov    %si,0x1a6(%rsp)               │  
  mov    %rax,%rsi                     │  
  mov    %rdx,0x198(%rsp)              │  
  mov    %r10,%rdx                     │  
  mov    %r8,0x190(%rsp)               │  
  call   *%r11                         │  
```  
So a hardcoded call of 0x0.  
I found this same pattern for different pieces of code in my codebase unrelated to multiprecision. So it's probably unrelated to this project.  
  
> That does coincide with some big changes to Boost's common exception handling support, is there any part of the application which hasn't been recompiled with the new Boost version? The ABI of the exception handling code changes radically in that range of commits.  
  
No, even gcc is compiled from scratch for the build.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2024-01-12 13:09:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1889157455  

So the call to memcpy is calling an invalid address?  zero?  That usually indicates that the standard library entry points haven't been correctly fixed up during program loading - which might be a GCC issue - what happens if you use the system supplied GCC?  Or do you need a bleeding edge version?

---

## Comment 15

> Username: gerharfa  
> Created at: 2024-01-12 22:55:29 UTC  
> Updated at: 2024-01-12 22:55:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-1890087801  

> So the call to memcpy is calling an invalid address? zero?  
  
yes  
  
> what happens if you use the system supplied GCC? Or do you need a bleeding edge version?  
  
im using 9.3.0 at the moment.   
  
my system supplied gcc is ~7 it doesn't support some of the features we use. I could try upgrading gcc, but that will probably come with its own set of issues.  
  
I might still try that though. But I'm hoping to understand why that asm was generated in the meantime.

---

## Comment 16

> Username: gerharfa  
> Created at: 2024-04-11 06:35:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/584#issuecomment-2049014555  

Closing this for now. I'll reopen if i have something you can actually reproduce. Sorry for wasting your time.
