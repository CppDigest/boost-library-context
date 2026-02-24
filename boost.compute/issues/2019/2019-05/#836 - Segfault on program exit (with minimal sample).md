# #836 - Segfault on program exit (with minimal sample) [Open]

> Username: rosenrodt  
> Created at: 2019-05-29 08:00:04 UTC  
> Updated at: 2019-05-31 08:55:30 UTC  
> Url: https://github.com/boostorg/compute/issues/836  

There seems to be an issue with current Nvidia and Intel platform (AMD does not seem to have this issue) when I compile Boost.Compute in a dynamic library; segfault is reported during some OpenCL resource cleanup while exiting. The interesting thing is if I statically link the same library this becomes a non-issue. **Update:** On MacOS it doesn't seem to have this issue  
  
I am able to reproduce this issue with a few lines of code ([here](https://github.com/rosenrodt/bug_opencl_boost_compute)). The site raising the exception might be a bit different (at `program::~program()` or `command_queue::~command_queue()` or `context::~context()`) if I added or removed a few boost::compute APIs here and there.  
  
Any thoughts on why this happens? I am hesitant to call this a Boost.Compute bug, but I have tried myself and I can't reproduce the problem in plain OpenCL API.  
  
A few notes:  
- The sample code uses exclusively default context and default command queue.   
- Tested on Boost 1.68.0 and current master branch at `36c89134`  
- OS: Win10   
  
Full stack trace 1 (Nvidia platform)  
```  
ntdll.dll!00007ffc9a8ae771()    Unknown  
tmmon64.dll!00000000617a0f2b()  Unknown  
nvopencl64.dll!00007ffc22e84bac()   Unknown  
nvopencl64.dll!00007ffc22a49562()   Unknown  
nvopencl64.dll!00007ffc22a38ece()   Unknown  
nvopencl64.dll!00007ffc22a38329()   Unknown  
nvopencl64.dll!00007ffc22a2de83()   Unknown  
nvopencl64.dll!00007ffc22a2e365()   Unknown  
nvopencl64.dll!00007ffc22a37afa()   Unknown  
mylib.dll!boost::compute::context::~context() Line 162  C++  
mylib.dll!`boost::compute::system::default_context'::`2'::`dynamic atexit destructor for 'default_context''()   C++  
ucrtbased.dll!00007ffc2fd30af7()    Unknown  
ucrtbased.dll!00007ffc2fd30545()    Unknown  
ucrtbased.dll!00007ffc2fd3064a()    Unknown  
ucrtbased.dll!00007ffc2fd30ca1()    Unknown  
mylib.dll!__scrt_dllmain_uninitialize_c() Line 392  C++  
mylib.dll!dllmain_crt_process_detach(const bool) Line 109   C++  
mylib.dll!dllmain_crt_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 134 C++  
mylib.dll!dllmain_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 209 C++  
mylib.dll!_DllMainCRTStartup(HINSTANCE__ * const, const unsigned long, void * const) Line 251   C++  
ntdll.dll!00007ffc9a8e50e7()    Unknown  
ntdll.dll!00007ffc9a91068c()    Unknown  
ntdll.dll!00007ffc9a91053d()    Unknown  
tmmon64.dll!000000006170698c()  Unknown  
kernel32.dll!00007ffc9853d05a() Unknown  
ucrtbased.dll!00007ffc2fd300da()    Unknown  
ucrtbased.dll!00007ffc2fd30099()    Unknown  
ucrtbased.dll!00007ffc2fd303d6()    Unknown  
myexe.exe!__scrt_common_main_seh() Line 275 C++  
myexe.exe!__scrt_common_main() Line 309 C++  
myexe.exe!mainCRTStartup() Line 17  C++  
kernel32.dll!00007ffc98537e94() Unknown  
ntdll.dll!00007ffc9a907ad1()    Unknown  
```  
  
Full stack trace 2 (Nvidia platform)  
```  
ntdll.dll!00007ffc9a8ae771()    Unknown  
tmmon64.dll!00000000617a0f2b()  Unknown  
nvopencl64.dll!00007ffc22e84bac()   Unknown  
nvopencl64.dll!00007ffc22bbc79d()   Unknown  
nvopencl64.dll!00007ffc22bbc697()   Unknown  
nvopencl64.dll!00007ffc22a3c4a2()   Unknown  
nvopencl64.dll!00007ffc22a2de8d()   Unknown  
nvopencl64.dll!00007ffc22a2e365()   Unknown  
nvopencl64.dll!00007ffc22a3c2a8()   Unknown  
mylib.dll!boost::compute::command_queue::~command_queue() Line 219  C++  
mylib.dll!`boost::compute::system::default_queue'::`2'::`dynamic atexit destructor for 'queue''()   C++  
ucrtbased.dll!00007ffc2fd30af7()    Unknown  
ucrtbased.dll!00007ffc2fd30545()    Unknown  
ucrtbased.dll!00007ffc2fd3064a()    Unknown  
ucrtbased.dll!00007ffc2fd30ca1()    Unknown  
mylib.dll!__scrt_dllmain_uninitialize_c() Line 392  C++  
mylib.dll!dllmain_crt_process_detach(const bool) Line 109   C++  
mylib.dll!dllmain_crt_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 134 C++  
mylib.dll!dllmain_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 209 C++  
mylib.dll!_DllMainCRTStartup(HINSTANCE__ * const, const unsigned long, void * const) Line 251   C++  
ntdll.dll!00007ffc9a8e50e7()    Unknown  
ntdll.dll!00007ffc9a91068c()    Unknown  
ntdll.dll!00007ffc9a91053d()    Unknown  
tmmon64.dll!000000006170698c()  Unknown  
kernel32.dll!00007ffc9853d05a() Unknown  
ucrtbased.dll!00007ffc2fd300da()    Unknown  
ucrtbased.dll!00007ffc2fd30099()    Unknown  
ucrtbased.dll!00007ffc2fd303d6()    Unknown  
myexe.exe!__scrt_common_main_seh() Line 275 C++  
myexe.exe!__scrt_common_main() Line 309 C++  
myexe.exe!mainCRTStartup() Line 17  C++  
kernel32.dll!00007ffc98537e94() Unknown  
ntdll.dll!00007ffc9a907ad1()    Unknown  
```  
  
Full stack trace 3 (Intel platform)  
```  
ucrtbase.dll!00007ffc96b2f08e() Unknown  
vcruntime140.dll!00007ffc87d94b16() Unknown  
intelocl64.dll!00007ffc2a7625e0()   Unknown  
intelocl64.dll!00007ffc2a75618d()   Unknown  
mylib.dll!boost::compute::command_queue::~command_queue() Line 219  C++  
mylib.dll!`boost::compute::system::default_queue'::`2'::`dynamic atexit destructor for 'queue''()   C++  
ucrtbased.dll!00007ffc2fd30af7()    Unknown  
ucrtbased.dll!00007ffc2fd30545()    Unknown  
ucrtbased.dll!00007ffc2fd3064a()    Unknown  
ucrtbased.dll!00007ffc2fd30ca1()    Unknown  
mylib.dll!__scrt_dllmain_uninitialize_c() Line 392  C++  
mylib.dll!dllmain_crt_process_detach(const bool) Line 109   C++  
mylib.dll!dllmain_crt_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 134 C++  
mylib.dll!dllmain_dispatch(HINSTANCE__ * const, const unsigned long, void * const) Line 209 C++  
mylib.dll!_DllMainCRTStartup(HINSTANCE__ * const, const unsigned long, void * const) Line 251   C++  
ntdll.dll!00007ffc9a8e50e7()    Unknown  
ntdll.dll!00007ffc9a91068c()    Unknown  
ntdll.dll!00007ffc9a91053d()    Unknown  
tmmon64.dll!000000006170698c()  Unknown  
kernel32.dll!00007ffc9853d05a() Unknown  
ucrtbased.dll!00007ffc2fd300da()    Unknown  
ucrtbased.dll!00007ffc2fd30099()    Unknown  
ucrtbased.dll!00007ffc2fd303d6()    Unknown  
myexe.exe!__scrt_common_main_seh() Line 275 C++  
myexe.exe!__scrt_common_main() Line 309 C++  
myexe.exe!mainCRTStartup() Line 17  C++  
kernel32.dll!00007ffc98537e94() Unknown  
ntdll.dll!00007ffc9a907ad1()    Unknown  
```

---

## Comment 1

> Username: rosenrodt  
> Created at: 2019-05-30 02:26:11 UTC  
> Updated at: 2019-05-30 08:52:26 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497177426  

I think I know what the problem is. And it is not related to Boost.Compute  
  
If you have a wrapper similar to `compute::context` and let the wrapper's call `clReleaseContext` upon destruction, define that wrapper in global scope or static and you can repro this issue.  
  
Mine is something like this  
```c++  
struct context_wrapper  
{  
  cl_context m_context;  
  context_wrapper() : m_context(0)  
  ~context_wrapper() { if (m_context) clReleaseContext(m_context); }  
  void attach_context(cl_context context) { m_context = context; }  
};  
  
context_wrapper gContext;  
  
void DLL_EXPORT func()  
{  
  cl_context context;  
  // initialize cl_context  
  // --------------------  
  // ...  
  // --------------------  
  gContext.attach_context(context);  
}  
```   
  
Call this imported DLL function and it gives you the segfault and also similar stack trace. I am still not certain why it refuses to exit program nice and clean, and how Boost.Compute can workaround this problem (if it is indeed confirmed by more people)  
  
EDIT: I should've made it clearer; calling the imported function *does not* give you segfault right away, but instead at program exit

---

## Comment 2

> Username: jszuppe  
> Created at: 2019-05-30 07:56:54 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497241590  

Thanks for the info. I'm not sure when I'll be able to look at it and debug it. Maybe in the weekend.

---

## Comment 3

> Username: jszuppe  
> Created at: 2019-05-30 08:06:05 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497244089  

Are you sure that `myexe` has access to OpenCL dynamic library?   
  
```cmake  
target_link_libraries(mylib PRIVATE  
    BoostCompute::BoostCompute  
)  
```  
Doesn't that commnad mean that `BoostCompute::BoostCompute` will not propagate to `OpenCL::OpenCL` to `myexe`?

---

## Comment 4

> Username: rosenrodt  
> Created at: 2019-05-30 08:47:41 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497255707  

> Doesn't that commnad mean that BoostCompute::BoostCompute will not propagate to OpenCL::OpenCL to myexe?  
  
It is intended that `myexe` has zero knowledge about OpenCL and Boost.Compute used by `mylib`

---

## Comment 5

> Username: rosenrodt  
> Created at: 2019-05-30 15:28:59 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497371721  

Some updates:  
  
My current approach for the driver bug is to call `clRetainContext()`, `clRetainDevice()`, and `clRetainCommandQueue()` for all `compute::default_xxx()`'s somewhere in the dynamic library. With that the segfault seems to go away.  
  
@jszuppe if you could independently verify this, how can we approach this issue for Boost.Compute, assuming the driver do not get the timely fix?

---

## Comment 6

> Username: rosenrodt  
> Created at: 2019-05-31 08:55:30 UTC  
> Url: https://github.com/boostorg/compute/issues/836#issuecomment-497632751  

Maybe we need to provide optionally something like `compute::system::release()` to release every statically-declared OpenCL resource from the OpenCL runtime. My latest finding is that it will also require clearing the global program cache in order to avoid the segfault.
