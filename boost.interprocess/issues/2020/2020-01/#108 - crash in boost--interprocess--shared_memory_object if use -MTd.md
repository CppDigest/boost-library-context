# #108 - crash in boost::interprocess::shared_memory_object if use /MTd [Closed]

> Username: cdannebe  
> Created at: 2020-01-17 09:28:08 UTC  
> Updated at: 2021-03-24 20:47:46 UTC  
> Closed at: 2021-03-24 20:47:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/108  

Hi,  
I have a simple project running under Windows. It consists of two DLLs and one EXE. The EXE loads the two DLLs. Each will create a shared memory object in a class instatiated by a std::unique_ptr globaly. The crash happens during FreeLibrary and only if I use the /MTd switch (Multi-threaded Debug) instead of /MDd (Multi-threaded Debug DLL).  
  
I built boost libs using:  
`b2.exe -a -j %NUMBER_OF_PROCESSORS% --prefix=%BOOST% --libdir=%BOOST%/lib/x86 --reconfigure --build-type=complete --toolset=msvc-14.2 link=static address-model=32 runtime-link=static runtime-link=shared threading=multi define=BOOST_USE_WINDOWS_H define=NOMINMAX install  
`  
where `%BOOST%` points to my BOOST installation folder.  
  
The example code can be downloaded [here](http://download.sinusmess.de/updates/SharedMemTest.7z).  
  
Kind regards and many thanks for your great work, Christian.

---

## Comment 1

> Username: cdannebe  
> Created at: 2020-01-17 11:48:02 UTC  
> Url: https://github.com/boostorg/interprocess/issues/108#issuecomment-575595272  

Hi,  
if I use /MTd the crash happens. But if I call FreeLibrary in reverse order to LoadLibrary the crash does not happen.  
The debugger shows me that the crash is always somewere in boost::interprocess.  
  
Christian

---

## Comment 2

> Username: fsmoke  
> Created at: 2020-01-17 16:29:13 UTC  
> Updated at: 2020-01-17 16:39:12 UTC  
> Url: https://github.com/boostorg/interprocess/issues/108#issuecomment-575697415  

I have got the same issue right now!  
  
Crash appears inside   
  
intermodule_singleton_common.hpp  
  
inside function  
```  
static void atexit_work()  
   {  
      intermodule_singleton_common<ThreadSafeGlobalMap>::finalize_singleton_logic  
         (this_module_singleton_ptr, this_module_singleton_initialized, singleton_destructor);  
   }  
```  
  
this function called twice - cos std::atexit - called twice inside 2 different dlls  
so two singletons were created - ...i suppose this sigletons use some shared resources(may be pointers) - as result new and delete called for different memory managers

---

## Comment 3

> Username: cdannebe  
> Created at: 2020-01-29 14:18:29 UTC  
> Updated at: 2020-01-31 07:06:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/108#issuecomment-579775545  

Hi,  
I think I solved the issue (maybe just for me). The problem isn't the `/MTd` switch of the compiler.  
I think the problem is related to the singleton implementation inside `boost::interprocess::shared_memory_object`. My project structure is as follows:  
  
An EXE file loads a DLL (a driver wrapper) which can load several device driver DLLs in parallel. Two of the driver DLLs are using their own `shared_memory_object`'s as described in the doc and it work in both cases. Until I call `FreeLibrary` for the driver DLLs. In that case always the second `FreeLibrary` crashes (but not with `/MDd` I didn't understand this seconds fact, sorry).  
  
I solved the issue by creating a `boost::interprocess::shared_memory_object` inside the driver wrapper DLL. I just instantiate such an object and in the next line I remove it. If I do this, my DLLs don't crash any more.  
  
I hope this helps the developer of `boost::interprocess`.  
  
Kind regards,  
Christian

---

## Comment 4

> Username: cdannebe  
> Created at: 2021-03-24 20:47:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/108#issuecomment-806176263  

My mistake was not related to boost or compiler flags nor something else of the above. I used thread synchronisation mechanisms during unloading the DLLs (PROCESS_DETACH in dll_main()). As mentioned in the Microsoft documentation this results in an undefined behaviour of the application! After redesigning my DLLs the shared_memory_objects and the entire software works stable. Maybe this helps someone.
