# #230 - `interprocess::ipcdetail::intermodule_singleton` not working on MinGW [Closed]

> Username: joaquintides  
> Created at: 2024-09-14 11:07:15 UTC  
> Updated at: 2024-10-19 07:31:53 UTC  
> Closed at: 2024-10-19 07:31:45 UTC  
> Url: https://github.com/boostorg/interprocess/issues/230  

As shown in [this CI job](https://github.com/boostorg/flyweight/actions/runs/10861565047/job/30143618327) from Boost.Flyweight:  
  
```  
2024-09-14T10:12:05.5807995Z gcc.compile.c++ bin.v2\libs\flyweight\test\test_intermod_holder.test\gcc-8\debug\x86_64\cxxstd-14-iso\threadapi-win32\threading-multi\visibility-hidden\test_intermod_holder.o  
2024-09-14T10:12:05.8093853Z gcc.compile.c++ bin.v2\libs\flyweight\test\test_no_locking.test\gcc-8\debug\x86_64\cxxstd-14-iso\threadapi-win32\threading-multi\visibility-hidden\test_no_locking_main.o  
2024-09-14T10:12:08.2337843Z gcc.link bin.v2\libs\flyweight\test\test_intermod_holder.test\gcc-8\debug\x86_64\cxxstd-14-iso\threadapi-win32\threading-multi\visibility-hidden\test_intermod_holder.exe  
2024-09-14T10:12:08.2709878Z testing.capture-output bin.v2\libs\flyweight\test\test_intermod_holder.test\gcc-8\debug\x86_64\cxxstd-14-iso\threadapi-win32\threading-multi\visibility-hidden\test_intermod_holder.run  
2024-09-14T10:12:08.2711770Z ====== BEGIN OUTPUT ======  
2024-09-14T10:12:08.2720797Z libs/flyweight/test/test_intermod_holder.cpp(33): test 'str==intermodule_flyweight_string("boost")' failed in function 'void test_intermodule_holder()'  
2024-09-14T10:12:08.2734316Z 1 error detected.  
```  
The failing test seems to indicate that the singleton is created anew in each module instead of shared among them. As you probably remember, `boost::flyweight::intermodule_holder` is a trivial wrapper over `interprocess::ipcdetail::intermodule_singleton`:  
  
https://github.com/boostorg/flyweight/blob/develop/include/boost/flyweight/intermodule_holder.hpp

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-10-19 07:31:45 UTC  
> Updated at: 2024-10-19 07:31:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/230#issuecomment-2423654759  

Duplicate of #236
