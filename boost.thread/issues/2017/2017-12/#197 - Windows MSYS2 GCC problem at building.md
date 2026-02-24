# #197 - Windows MSYS2 GCC problem at building [Closed]

> Username: Kojoley  
> Created at: 2017-12-23 00:23:46 UTC  
> Updated at: 2018-04-18 15:46:48 UTC  
> Closed at: 2018-04-18 15:42:00 UTC  
> Url: https://github.com/boostorg/thread/issues/197  

Microsoft Windows [Version 6.1.7601]  
gcc (GCC) 6.4.0  
  
Run from Boost.Thread test folder:  
```  
$ ../../../b2.exe -j2 toolset=gcc test_thread  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...found 3098 targets...  
...updating 42 targets...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thread.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\winrt_init.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o  
..\..\..\libs\thread\src\win32\thread.cpp: In function ‘void boost::{anonymous}::create_current_thread_tls_key()’:  
..\..\..\libs\thread\src\win32\thread.cpp:84:37: error: ‘tss_cleanup_implemented’ was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘bool boost::thread::start_thread_noexcept()’:  
..\..\..\libs\thread\src\win32\thread.cpp:322:127: error: ‘struct boost::detail::thread_data_base’ has no member named ‘id’  
         uintptr_t const new_thread=_beginthreadex(0,0,&thread_start_function,thread_info.get(),CREATE_SUSPENDED,&thread_info->id);  
                                                                                                                               ^~  
..\..\..\libs\thread\src\win32\thread.cpp:323:13: error: in argument to unary !  
         if(!new_thread)  
             ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:327:48: error: ‘intrusive_ptr_add_ref’ was not declared in this scope  
         intrusive_ptr_add_ref(thread_info.get());  
                                                ^  
..\..\..\libs\thread\src\win32\thread.cpp:328:45: error: ‘boost::detail::win32’ has not been declared  
         thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                             ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘bool boost::thread::start_thread_noexcept(const attributes&)’:  
..\..\..\libs\thread\src\win32\thread.cpp:342:117: error: ‘struct boost::detail::thread_data_base’ has no member named ‘id’  
                                                 CREATE_SUSPENDED | STACK_SIZE_PARAM_IS_A_RESERVATION, &thread_info->id);  
                                                                                                                     ^~  
..\..\..\libs\thread\src\win32\thread.cpp:343:11: error: in argument to unary !  
       if(!new_thread)  
           ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:347:46: error: ‘intrusive_ptr_add_ref’ was not declared in this scope  
       intrusive_ptr_add_ref(thread_info.get());  
                                              ^  
..\..\..\libs\thread\src\win32\thread.cpp:348:43: error: ‘boost::detail::win32’ has not been declared  
       thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                           ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In constructor ‘boost::{anonymous}::externally_launched_thread::externally_launched_thread()’:  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: error: ‘count’ was not declared in this scope  
                 ++count;  
                   ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: note: suggested alternative:  
In file included from /usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/algorithm:62:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:39,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/stl_algo.h:3961:5: note:   ‘std::count’  
     count(_InputIterator __first, _InputIterator __last, const _Tp& __value)  
     ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: error: ‘interruption_enabled’ was not declared in this scope  
                 interruption_enabled=false;  
                 ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: note: suggested alternative:  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:624:32: note:   ‘boost::this_thread::interruption_enabled’  
         bool BOOST_THREAD_DECL interruption_enabled() BOOST_NOEXCEPT;  
                                ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:414:16: error: redefinition of ‘boost::thread::id boost::thread::get_id() const’  
     thread::id thread::get_id() const BOOST_NOEXCEPT  
                ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:748:16: note: ‘boost::thread::id boost::thread::get_id() const’ previously defined here  
     thread::id thread::get_id() const BOOST_NOEXCEPT  
                ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘bool boost::thread::join_noexcept()’:  
..\..\..\libs\thread\src\win32\thread.cpp:442:13: error: ‘interruptible_wait’ is not a member of ‘boost::this_thread’  
             this_thread::interruptible_wait(this->native_handle(),detail::timeout::sentinel());  
             ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:442:75: error: ‘boost::detail::timeout’ has not been declared  
             this_thread::interruptible_wait(this->native_handle(),detail::timeout::sentinel());  
                                                                           ^~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:453:10: error: redefinition of ‘bool boost::thread::timed_join(const system_time&)’  
     bool thread::timed_join(boost::system_time const& wait_until)  
          ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:540:14: note: ‘bool boost::thread::timed_join(const system_time&)’ previously defined here  
         bool timed_join(const system_time& abs_time)  
              ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:458:10: error: prototype for ‘bool boost::thread::do_try_join_until_noexcept(uintmax_t, bool&)’ does not match any in class ‘boost::thread’  
     bool thread::do_try_join_until_noexcept(uintmax_t milli, bool& res)  
          ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:536:14: error: candidate is: bool boost::thread::do_try_join_until_noexcept(const timespec&, bool&)  
         bool do_try_join_until_noexcept(struct timespec const &timeout, bool& res);  
              ^~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘void boost::thread::interrupt()’:  
..\..\..\libs\thread\src\win32\thread.cpp:494:32: error: ‘struct boost::detail::thread_data_base’ has no member named ‘interrupt’  
             local_thread_info->interrupt();  
                                ^~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘bool boost::thread::interruption_requested() const’:  
..\..\..\libs\thread\src\win32\thread.cpp:501:44: error: ‘winapi’ has not been declared  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                            ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:501:93: error: ‘struct boost::detail::thread_data_base’ has no member named ‘interruption_handle’; did you mean ‘interrupt_enabled’?  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                                                                             ^~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In static member function ‘static unsigned int boost::thread::hardware_concurrency()’:  
..\..\..\libs\thread\src\win32\thread.cpp:508:17: error: ‘boost::detail::win32’ has not been declared  
         detail::win32::system_info info;  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:509:17: error: ‘boost::detail::win32’ has not been declared  
         detail::win32::get_system_info(&info);  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:509:41: error: ‘info’ was not declared in this scope  
         detail::win32::get_system_info(&info);  
                                         ^~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function ‘__pthread_t* boost::thread::native_handle()’:  
..\..\..\libs\thread\src\win32\thread.cpp:547:28: error: ‘boost::detail::win32’ has not been declared  
             return detail::win32::invalid_handle_value;  
                            ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:553:25: error: ‘boost::detail::win32’ has not been declared  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:553:39: error: expected ‘;’ before ‘local_thread_info’  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                                       ^~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:566:40: error: ‘timeout’ is not a member of ‘boost::detail’  
             LARGE_INTEGER get_due_time(detail::timeout const&  target_time)  
                                        ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:567:13: error: expected ‘,’ or ‘;’ before ‘{’ token  
             {  
             ^  
..\..\..\libs\thread\src\win32\thread.cpp:1018:1: error: expected ‘}’ at end of input  
 }  
 ^  
..\..\..\libs\thread\src\win32\thread.cpp:1018:1: error: expected ‘}’ at end of input  
..\..\..\libs\thread\src\win32\thread.cpp:1018:1: error: expected ‘}’ at end of input  
In file included from ..\..\../boost/smart_ptr/shared_ptr.hpp:28:0,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/shared_count.hpp: In instantiation of ‘boost::detail::shared_count::shared_count(P, D) [with P = boost::detail::thread_data_base*; D = bool]’:  
..\..\../boost/smart_ptr/shared_ptr.hpp:388:76:   required from ‘boost::shared_ptr<T>::shared_ptr(Y*, D) [with Y = boost::detail::thread_data_base; D = bool; T = boost::detail::thread_data_base]’  
..\..\..\libs\thread\src\win32\thread.cpp:255:96:   required from here  
..\..\../boost/smart_ptr/detail/shared_count.hpp:185:14: error: ‘d’ cannot be used as a function  
             d(p); // delete p  
             ~^~~  
In file included from ..\..\../boost/smart_ptr/detail/shared_count.hpp:30:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:28,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of ‘void boost::detail::sp_counted_impl_pd<P, D>::dispose() [with P = boost::detail::thread_data_base*; D = bool]’:  
..\..\..\libs\thread\src\win32\thread.cpp:1018:1:   required from here  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp:172:9: error: expression cannot be used as a function  
         del( ptr );  
         ^~~  
..\..\..\libs\thread\src\win32\thread.cpp:402:35: warning: ‘boost::detail::thread_data_base* boost::{anonymous}::get_or_make_current_thread_data()’ defined but not used [-Wunused-function]  
         detail::thread_data_base* get_or_make_current_thread_data()  
                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:91:14: warning: ‘void boost::{anonymous}::cleanup_tls_key()’ defined but not used [-Wunused-function]  
         void cleanup_tls_key()  
              ^~~~~~~~~~~~~~~  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -mthreads -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o" "..\..\..\libs\thread\src\win32\thread.cpp"  
  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>libboost_thread-gcc64-mt-d-x64-1_67.dll.a for lack of <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>win32\thread.o...  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-20 17:08:55 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367047519  

Please, could you provide the result of the pre-processor (option -E)?

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-02-20 17:22:41 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367051892  

[thread.o.zip](https://github.com/boostorg/thread/files/1741130/thread.o.zip)

---

## Comment 3

> Username: viboes  
> Created at: 2018-02-20 17:35:29 UTC  
> Updated at: 2018-02-20 17:43:53 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367055936  

The zip file contains pthread code.  
  
```  
# 1 "..\\..\\../boost/thread/pthread/thread_data.hpp" 1  
  
```  
  
I guess that we don't deduce that the platform is WIN32 correctly on MSYS2.  
Please, could you try with   
```  
../../../b2.exe -j2 toolset=gcc threadapi=win32 test_thread  
```  
  
Be sure that BOOST_THREAD_PLATFORM_WIN32 is defined in file `boost/thread/thread_only.hpp`  
  
```  
#if defined(BOOST_THREAD_PLATFORM_WIN32)  
#include <boost/thread/win32/thread_data.hpp>  
#elif defined(BOOST_THREAD_PLATFORM_PTHREAD)  
#include <boost/thread/pthread/thread_data.hpp>  
#else  
#error "Boost threads unavailable on this platform"  
#endif  
```

---

## Comment 4

> Username: Kojoley  
> Created at: 2018-02-20 17:41:16 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367057791  

> Please, could you try with  
> `../../../b2.exe -j2 toolset=gcc threadapi=win32 test_thread`  
  
The result of this output is in the topic, but if you insist...  
```  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$ ../../../b2.exe -j2 toolset=gcc threadapi=win32 test_thread  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
...patience...  
...patience...  
...found 3100 targets...  
...updating 82 targets...  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\chrono.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\winrt_init.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\thread_clock.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\process_cpu_clocks.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thread.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\tss_dll.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\tss_pe.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\future.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\error_code.o  
gcc.link.dll ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\libboost_chrono-gcc64-mt-d-x64-1_67.dll.a  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o  
..\..\..\libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::create_current_thread_tls_key()':  
..\..\..\libs\thread\src\win32\thread.cpp:84:37: error: 'tss_cleanup_implemented' was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:322:127: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
         uintptr_t const new_thread=_beginthreadex(0,0,&thread_start_function,thread_info.get(),CREATE_SUSPENDED,&thread_info->id);  
                                                                                                                               ^~  
..\..\..\libs\thread\src\win32\thread.cpp:323:13: error: in argument to unary !  
         if(!new_thread)  
             ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:327:48: error: 'intrusive_ptr_add_ref' was not declared in this scope  
         intrusive_ptr_add_ref(thread_info.get());  
                                                ^  
..\..\..\libs\thread\src\win32\thread.cpp:328:45: error: 'boost::detail::win32' has not been declared  
         thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                             ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept(const attributes&)':  
..\..\..\libs\thread\src\win32\thread.cpp:342:117: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
                                                 CREATE_SUSPENDED | STACK_SIZE_PARAM_IS_A_RESERVATION, &thread_info->id);  
                                                                                                                     ^~  
..\..\..\libs\thread\src\win32\thread.cpp:343:11: error: in argument to unary !  
       if(!new_thread)  
           ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:347:46: error: 'intrusive_ptr_add_ref' was not declared in this scope  
       intrusive_ptr_add_ref(thread_info.get());  
                                              ^  
..\..\..\libs\thread\src\win32\thread.cpp:348:43: error: 'boost::detail::win32' has not been declared  
       thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                           ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In constructor 'boost::{anonymous}::externally_launched_thread::externally_launched_thread()':  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: error: 'count' was not declared in this scope  
                 ++count;  
                   ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: note: suggested alternative:  
In file included from /usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/algorithm:62:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:39,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/stl_algo.h:3961:5: note:   'std::count'  
     count(_InputIterator __first, _InputIterator __last, const _Tp& __value)  
     ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: error: 'interruption_enabled' was not declared in this scope  
                 interruption_enabled=false;  
                 ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: note: suggested alternative:  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:592:32: note:   'boost::this_thread::interruption_enabled'  
         bool BOOST_THREAD_DECL interruption_enabled() BOOST_NOEXCEPT;  
                                ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:414:16: error: redefinition of 'boost::thread::id boost::thread::get_id() const'  
     thread::id thread::get_id() const BOOST_NOEXCEPT  
                ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:716:23: note: 'boost::thread::id boost::thread::get_id() const' previously defined here  
     inline thread::id thread::get_id() const BOOST_NOEXCEPT  
                       ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::join_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:442:13: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
             this_thread::interruptible_wait(this->native_handle(), detail::internal_platform_timepoint::getMax());  
             ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::do_try_join_until_noexcept(const internal_platform_timepoint&, bool&)':  
..\..\..\libs\thread\src\win32\thread.cpp:457:15: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
           if(!this_thread::interruptible_wait(this->native_handle(), timeout))  
               ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'void boost::thread::interrupt()':  
..\..\..\libs\thread\src\win32\thread.cpp:488:32: error: 'struct boost::detail::thread_data_base' has no member named 'interrupt'  
             local_thread_info->interrupt();  
                                ^~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::interruption_requested() const':  
..\..\..\libs\thread\src\win32\thread.cpp:495:44: error: 'winapi' has not been declared  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                            ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:495:93: error: 'struct boost::detail::thread_data_base' has no member named 'interruption_handle'; did you mean 'interrupt_enabled'?  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                                                                             ^~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In static member function 'static unsigned int boost::thread::hardware_concurrency()':  
..\..\..\libs\thread\src\win32\thread.cpp:502:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::system_info info;  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::get_system_info(&info);  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:41: error: 'info' was not declared in this scope  
         detail::win32::get_system_info(&info);  
                                         ^~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function '__pthread_t* boost::thread::native_handle()':  
..\..\..\libs\thread\src\win32\thread.cpp:541:28: error: 'boost::detail::win32' has not been declared  
             return detail::win32::invalid_handle_value;  
                            ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:25: error: 'boost::detail::win32' has not been declared  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:39: error: expected ';' before 'local_thread_info'  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                                       ^~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:608:41: error: 'boost::detail::win32' has not been declared  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:111: error: expected primary-expression before 'const'  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                               ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:125: error: expression list treated as compound expression in initializer [-fpermissive]  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                                             ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
 }  
 ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
In file included from ..\..\../boost/smart_ptr/shared_ptr.hpp:28:0,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/shared_count.hpp: In instantiation of 'boost::detail::shared_count::shared_count(P, D) [with P = boost::detail::thread_data_base*; D = bool]':  
..\..\../boost/smart_ptr/shared_ptr.hpp:388:76:   required from 'boost::shared_ptr<T>::shared_ptr(Y*, D) [with Y = boost::detail::thread_data_base; D = bool; T = boost::detail::thread_data_base]'  
..\..\..\libs\thread\src\win32\thread.cpp:255:96:   required from here  
..\..\../boost/smart_ptr/detail/shared_count.hpp:185:14: error: 'd' cannot be used as a function  
             d(p); // delete p  
             ~^~~  
In file included from ..\..\../boost/smart_ptr/detail/shared_count.hpp:30:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:28,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of 'void boost::detail::sp_counted_impl_pd<P, D>::dispose() [with P = boost::detail::thread_data_base*; D = bool]':  
..\..\..\libs\thread\src\win32\thread.cpp:972:1:   required from here  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp:172:9: error: expression cannot be used as a function  
         del( ptr );  
         ^~~  
..\..\..\libs\thread\src\win32\thread.cpp:402:35: warning: 'boost::detail::thread_data_base* boost::{anonymous}::get_or_make_current_thread_data()' defined but not used [-Wunused-function]  
         detail::thread_data_base* get_or_make_current_thread_data()  
                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:91:14: warning: 'void boost::{anonymous}::cleanup_tls_key()' defined but not used [-Wunused-function]  
         void cleanup_tls_key()  
              ^~~~~~~~~~~~~~~  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -mthreads -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_USE_WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o" "..\..\..\libs\thread\src\win32\thread.cpp"  
  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>libboost_thread-gcc64-mt-d-x64-1_67.dll.a for lack of <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>win32\thread.o...  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\compiler_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\debug.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\execution_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\decorator.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\plain_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\progress_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\framework.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\results_collector.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_framework_init_observer.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\results_reporter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_tools.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_tree.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\unit_test_log.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\unit_test_main.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\unit_test_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\junit_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\xml_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\unit_test_parameters.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\xml_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\auto_timers_construction.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\cpu_timer.o  
gcc.link.dll ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\libboost_timer-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.exe for lack of <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>libboost_thread-gcc64-mt-d-x64-1_67.dll.a...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.run for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.exe...  
...failed updating 1 target...  
...skipped 4 targets...  
...updated 76 targets...  
```

---

## Comment 5

> Username: viboes  
> Created at: 2018-02-20 18:19:55 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367069791  

I requested to add `threadapi=win32` which was not the case for the first report.  
  
Could you try  
```  
#include <boost/config.hpp>  
```  
  
and check that one of `defined(_WIN32) || defined(__WIN32__) || defined(WIN32)`are defined.  
  
The concerned code in in `boost/thread/detail/platform.hpp`   
```  
// fetch compiler and platform configuration  
#include <boost/config.hpp>  
  
// insist on threading support being available:  
#include <boost/config/requires_threads.hpp>  
  
// choose platform  
#if defined(linux) || defined(__linux) || defined(__linux__)  
#  define BOOST_THREAD_LINUX  
//#  define BOOST_THREAD_WAIT_BUG boost::posix_time::microseconds(100000)  
#elif defined(__FreeBSD__) || defined(__NetBSD__) || defined(__OpenBSD__) || defined(__DragonFly__)  
#  define BOOST_THREAD_BSD  
#elif defined(sun) || defined(__sun)  
#  define BOOST_THREAD_SOLARIS  
#elif defined(__sgi)  
#  define BOOST_THREAD_IRIX  
#elif defined(__hpux)  
#  define BOOST_THREAD_HPUX  
#elif defined(__CYGWIN__)  
#  define BOOST_THREAD_CYGWIN  
#elif (defined(_WIN32) || defined(__WIN32__) || defined(WIN32)) && !defined(BOOST_DISABLE_WIN32)  
#  define BOOST_THREAD_WIN32  
```

---

## Comment 6

> Username: viboes  
> Created at: 2018-02-20 18:30:35 UTC  
> Updated at: 2018-02-20 18:36:54 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367073218  

I'm not an expert on Windows, but Boost/Config is viewing it as cygwin. Is this what you expect?  
  
  
```  
# 1 "..\\..\\../boost/config/detail/select_platform_config.hpp" 1  
# 54 "..\\..\\../boost/config.hpp" 2  
  
  
  
# 1 "..\\..\\../boost/config/platform/cygwin.hpp" 1  
# 46 "..\\..\\../boost/config/platform/cygwin.hpp"  
# 1 "..\\..\\../boost/config/detail/posix_features.hpp" 1  
# 47 "..\\..\\../boost/config/platform/cygwin.hpp" 2  
```  
  
cygwin is not considered as a WIN32 platform in Boost.Thread, as it has BOOST_HAS_PTHREADS, isn't it?  
  
```  
#if defined(BOOST_THREAD_POSIX)  
#  define BOOST_THREAD_PLATFORM_PTHREAD  
#else  
#  if defined(BOOST_THREAD_WIN32)  
#       define BOOST_THREAD_PLATFORM_WIN32  
#  elif defined(BOOST_HAS_PTHREADS)  
#       define BOOST_THREAD_PLATFORM_PTHREAD  
#  else  
#       error "Sorry, no boost threads are available for this platform."  
#  endif  
#endif  
```  
  
It seems we have that the build system is detecting threadapi=win32 and Boost.Thread consider that it has a pthread.  
  
Could you try `threadapi=pthread`and verbose `-d2`  
```  
 ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread  
```

---

## Comment 7

> Username: viboes  
> Created at: 2018-02-20 18:40:55 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367076454  

Maybe you can try also to add in file `thread/build/Jamfile.v2` after   
  
```  
rule usage-requirements ( properties * )  
{  
    local result ;  
    if <threadapi>pthread in $(properties)  
    {  
        result += <define>BOOST_THREAD_POSIX ;  
        if <target-os>windows in $(properties)  
        {  
            result += [ win32_pthread_paths $(properties) ] ;  
            # TODO: What is for static linking? Is the <library> also needed  
            # in that case?  
        }  
    }  
  
```  
  
the following  
```  
    if <threadapi>win32 in $(properties)  
    {  
        result += <define>BOOST_THREAD_WIN32 ;  
    }  
```

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-02-20 18:57:17 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367081295  

> I requested to add `threadapi=win32` which was not the case for the first report.  
  
Is not it default? Look at the path `gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thread.o`  
  
> Could you try  
> `#include <boost/config.hpp>`  
  
```cpp  
#include <boost/config.hpp>  
#include <iostream>  
  
int main()  
{  
    std::cout << "BOOST_PLATFORM: " << BOOST_PLATFORM << std::endl;  
  
#if defined(_WIN32) || defined(__WIN32__) || defined(WIN32)  
    std::cout << "WIN32: Yes" << std::endl;  
#else  
    std::cout << "WIN32: No" << std::endl;  
#endif  
  
#if defined(BOOST_WINDOWS)  
    std::cout << "BOOST_WINDOWS: Yes" << std::endl;  
#else  
    std::cout << "BOOST_WINDOWS: No" << std::endl;  
#endif  
  
#if defined(__MINGW32__) || defined(__MINGW64__)  
    std::cout << "MINGW: Yes" << std::endl;  
#else  
    std::cout << "MINGW: No" << std::endl;  
#endif  
  
#if defined(__CYGWIN__)  
    std::cout << "CYGWIN: Yes" << std::endl;  
#else  
    std::cout << "CYGWIN: No" << std::endl;  
#endif  
  
    return 1;  
}  
```  
  
```  
BOOST_PLATFORM: Cygwin  
WIN32: No  
BOOST_WINDOWS: No  
MINGW: No  
CYGWIN: Yes  
```  
  
> I'm not an expert on Windows, but Boost/Config is viewing it as cygwin. Is this what you expect?  
  
It is a good question. Actually I was expecting a MinGW, but it looks like by default it is treats itself like a Cygwin (with MSYSTEM=MSYS) https://github.com/msys2/msys2/wiki/How-does-MSYS2-differ-from-Cygwin  
  
> cygwin is not considered as a WIN32 platform in Boost.Thread, as it has BOOST_HAS_PTHREADS, isn't it?  
> Could you try threadapi=pthread  
  
```  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$  ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
************************************************************  
Trying to build Boost.Thread with pthread support.  
If you need pthread you should specify the paths.  
You can specify them in site-config.jam, user-config.jam  
or in the environment.  
For example:  
PTW32_INCLUDE=C:\Program Files\ptw32\Pre-built2\include  
PTW32_LIB=C:\Program Files\ptw32\Pre-built2\lib  
************************************************************  
    - lockfree boost::atomic_flag : yes  
...patience...  
...found 2281 targets...  
...updating 49 targets...  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\compiler_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\winrt_init.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\debug.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\decorator.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\execution_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\plain_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\progress_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\results_collector.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\framework.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_framework_init_observer.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\results_reporter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_tools.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_tree.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_log.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_main.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\junit_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\xml_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\xml_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_parameters.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\auto_timers_construction.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\chrono.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\thread_clock.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\cpu_timer.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\process_cpu_clocks.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\error_code.o  
gcc.link.dll ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_chrono-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_timer-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a  
gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `interruption_point_thread(boost::mutex*, bool*)':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:83: undefined reference to `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:83:(.text+0x1cab): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_thread_interrupts_at_interruption_point()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:93: undefined reference to `boost::thread::interrupt()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:93:(.text+0x1d50): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::interrupt()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `disabled_interruption_point_thread(boost::mutex*, bool*)':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x2452): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:108: undefined reference to `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:108:(.text+0x2457): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x246a): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x2487): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::~disable_interruption()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_thread_no_interrupt_if_interrupts_disabled_at_interruption_point()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:118: undefined reference to `boost::thread::interrupt()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:118:(.text+0x2514): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::interrupt()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_timed_join()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:182: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:182:(.text+0x33aa): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:187: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:187:(.text+0x37a5): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:199: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:199:(.text+0x3bc0): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::interruption_checker::interruption_checker(__pthread_mutex_t**, __pthread_cond_t**)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/thread_data.hpp:199: undefined reference to `boost::detail::get_current_thread_data()'  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/thread_data.hpp:199:(.text$_ZN5boost6detail20interruption_checkerC1EPP17__pthread_mutex_tPP16__pthread_cond_t[_ZN5boost6detail20interruption_checkerC1EPP17__pthread_mutex_tPP16__pthread_cond_t]+0x1b): additional relocation overflows omitted from the output  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::start_thread()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:179: undefined reference to `boost::thread::start_thread_noexcept()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::~thread()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:254: undefined reference to `boost::thread::detach()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::get_id() const':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:719: undefined reference to `boost::thread::native_handle()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::join()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:743: undefined reference to `boost::thread::join_noexcept()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::do_try_join_until(boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:753: undefined reference to `boost::thread::do_try_join_until_noexcept(boost::detail::mono_platform_timepoint const&, bool&)'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::condition_variable::wait(boost::unique_lock<boost::mutex>&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:94: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::condition_variable::do_wait_until(boost::unique_lock<boost::mutex>&, boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:137: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `bool boost::condition_variable_any::do_wait_until<boost::unique_lock<boost::mutex> >(boost::unique_lock<boost::mutex>&, boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:485: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<void (*)()>::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::_bi::bind_t<void, void (*)(boost::thread::id), boost::_bi::list1<boost::_bi::value<boost::thread::id> > > >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::_bi::bind_t<void, void (*)(boost::mutex*, bool*), boost::_bi::list2<boost::_bi::value<boost::mutex*>, boost::_bi::value<bool*> > > >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::reference_wrapper<non_copyable_functor> >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:125: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::reference_wrapper<long_running_thread> >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:125: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o:/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: more undefined references to `boost::detail::thread_data_base::~thread_data_base()' follow  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o:test_thread.cpp:(.rdata$.refptr._ZTVN5boost6detail16thread_data_baseE[.refptr._ZTVN5boost6detail16thread_data_baseE]+0x0): undefined reference to `vtable for boost::detail::thread_data_base'  
collect2: error: ld returned 1 exit status  
  
    "g++"  -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe" -Wl,--start-group "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o" "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\winrt_init.o" "..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a" "..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -g -mthreads -m64  
  
...failed gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi>test_thread.run for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi>test_thread.exe...  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 46 targets...  
```

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-02-20 19:10:31 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367085258  

> Maybe you can try also to add in file thread/build/Jamfile.v2 after  
> ```  
> rule usage-requirements ( properties * )  
> {  
>     local result ;  
>     if <threadapi>pthread in $(properties)  
>     {  
>         result += <define>BOOST_THREAD_POSIX ;  
>         if <target-os>windows in $(properties)  
>         {  
>             result += [ win32_pthread_paths $(properties) ] ;  
>             # TODO: What is for static linking? Is the <library> also needed  
>             # in that case?  
>         }  
>    }  
> ```  
> the following  
> ```  
>     if <threadapi>win32 in $(properties)  
>     {  
>         result += <define>BOOST_THREAD_WIN32 ;  
>     }  
> ```  
```  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread_lib.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thread.o  
In file included from ..\..\../boost/thread/win32/thread_data.hpp:11:0,  
                 from ..\..\../boost/thread/thread_only.hpp:15,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/win32/thread_primitives.hpp:51:38: error: expected initializer before 'ticks_type'  
             typedef unsigned __int64 ticks_type;  
                                      ^~~~~~~~~~  
..\..\../boost/thread/win32/thread_primitives.hpp:75:52: error: typedef 'boost::detail::win32::detail::ticks_type' is initialized (use declt            ype instead)  
             namespace detail { typedef ticks_type (__stdcall *gettickcount64_t)(); }  
                                                    ^  
..\..\../boost/thread/win32/thread_primitives.hpp:75:52: error: expected primary-expression before '__attribute__'  
             namespace detail { typedef ticks_type (__stdcall *gettickcount64_t)(); }  
                                                    ^  
In file included from ..\..\../boost/thread/win32/thread_data.hpp:11:0,  
                 from ..\..\../boost/thread/thread_only.hpp:15,  
                 from test_thread.cpp:13:  
..\..\../boost/thread/win32/thread_primitives.hpp:90:20: error: 'ticks_type' does not name a type  
             inline ticks_type __stdcall GetTickCount64emulation()  
                    ^~~~~~~~~~  
..\..\../boost/thread/win32/thread_primitives.hpp:140:28: error: 'gettickcount64_t' in namespace 'boost::detail::win32::detail' does not nam            e a type  
             inline detail::gettickcount64_t GetTickCount64_()  
                            ^~~~~~~~~~~~~~~~  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -mthreads -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOO            ST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THR            EAD_THROW_IF_PRECONDITION_NOT_SATISFIED -DBOOST_THREAD_USE_LIB=1 -DBOOST_THREAD_WIN32 -DBOOST_TIMER_STATIC_LINK=1  -I"..\..\.." -c -o "..\..            \..\bin.v2\libs\thread\test\test_thread_lib.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thread.o" "test_thread.cpp"  
  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread_lib.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\test_thr            ead.o...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread_lib.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\winrt_init.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread_lib.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\tss_null.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o  
..\..\..\libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::create_current_thread_tls_key()':  
..\..\..\libs\thread\src\win32\thread.cpp:84:37: error: 'tss_cleanup_implemented' was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:322:127: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
         uintptr_t const new_thread=_beginthreadex(0,0,&thread_start_function,thread_info.get(),CREATE_SUSPENDED,&thread_info->id);  
                                                                                                                               ^~  
..\..\..\libs\thread\src\win32\thread.cpp:323:13: error: in argument to unary !  
         if(!new_thread)  
             ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:327:48: error: 'intrusive_ptr_add_ref' was not declared in this scope  
         intrusive_ptr_add_ref(thread_info.get());  
                                                ^  
..\..\..\libs\thread\src\win32\thread.cpp:328:45: error: 'boost::detail::win32' has not been declared  
         thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                             ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept(const attributes&)':  
..\..\..\libs\thread\src\win32\thread.cpp:342:117: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
                                                 CREATE_SUSPENDED | STACK_SIZE_PARAM_IS_A_RESERVATION, &thread_info->id);  
                                                                                                                     ^~  
..\..\..\libs\thread\src\win32\thread.cpp:343:11: error: in argument to unary !  
       if(!new_thread)  
           ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:347:46: error: 'intrusive_ptr_add_ref' was not declared in this scope  
       intrusive_ptr_add_ref(thread_info.get());  
                                              ^  
..\..\..\libs\thread\src\win32\thread.cpp:348:43: error: 'boost::detail::win32' has not been declared  
       thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                           ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In constructor 'boost::{anonymous}::externally_launched_thread::externally_launched_thread()':  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: error: 'count' was not declared in this scope  
                 ++count;  
                   ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: note: suggested alternative:  
In file included from /usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/algorithm:62:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:39,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/stl_algo.h:3961:5: note:   'std::count'  
     count(_InputIterator __first, _InputIterator __last, const _Tp& __value)  
     ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: error: 'interruption_enabled' was not declared in this scope  
                 interruption_enabled=false;  
                 ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: note: suggested alternative:  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:592:32: note:   'boost::this_thread::interruption_enabled'  
         bool BOOST_THREAD_DECL interruption_enabled() BOOST_NOEXCEPT;  
                                ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:414:16: error: redefinition of 'boost::thread::id boost::thread::get_id() const'  
     thread::id thread::get_id() const BOOST_NOEXCEPT  
                ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:716:23: note: 'boost::thread::id boost::thread::get_id() const' previously defined here  
     inline thread::id thread::get_id() const BOOST_NOEXCEPT  
                       ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::join_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:442:13: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
             this_thread::interruptible_wait(this->native_handle(), detail::internal_platform_timepoint::getMax());  
             ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::do_try_join_until_noexcept(const internal_platform_timepo            int&, bool&)':  
..\..\..\libs\thread\src\win32\thread.cpp:457:15: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
           if(!this_thread::interruptible_wait(this->native_handle(), timeout))  
               ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'void boost::thread::interrupt()':  
..\..\..\libs\thread\src\win32\thread.cpp:488:32: error: 'struct boost::detail::thread_data_base' has no member named 'interrupt'  
             local_thread_info->interrupt();  
                                ^~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::interruption_requested() const':  
..\..\..\libs\thread\src\win32\thread.cpp:495:44: error: 'winapi' has not been declared  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                            ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:495:93: error: 'struct boost::detail::thread_data_base' has no member named 'interruption_handle';             did you mean 'interrupt_enabled'?  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                                                                             ^~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In static member function 'static unsigned int boost::thread::hardware_concurrency()':  
..\..\..\libs\thread\src\win32\thread.cpp:502:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::system_info info;  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::get_system_info(&info);  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:41: error: 'info' was not declared in this scope  
         detail::win32::get_system_info(&info);  
                                         ^~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function '__pthread_t* boost::thread::native_handle()':  
..\..\..\libs\thread\src\win32\thread.cpp:541:28: error: 'boost::detail::win32' has not been declared  
             return detail::win32::invalid_handle_value;  
                            ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:25: error: 'boost::detail::win32' has not been declared  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:39: error: expected ';' before 'local_thread_info'  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                                       ^~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:608:41: error: 'boost::detail::win32' has not been declared  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:111: error: expected primary-expression before 'const'  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                               ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:125: error: expression list treated as compound expression in initializer [-fpermissive]  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                                             ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
 }  
 ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
In file included from ..\..\../boost/smart_ptr/shared_ptr.hpp:28:0,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/shared_count.hpp: In instantiation of 'boost::detail::shared_count::shared_count(P, D) [with P = boost::deta            il::thread_data_base*; D = bool]':  
..\..\../boost/smart_ptr/shared_ptr.hpp:388:76:   required from 'boost::shared_ptr<T>::shared_ptr(Y*, D) [with Y = boost::detail::thread_dat            a_base; D = bool; T = boost::detail::thread_data_base]'  
..\..\..\libs\thread\src\win32\thread.cpp:255:96:   required from here  
..\..\../boost/smart_ptr/detail/shared_count.hpp:185:14: error: 'd' cannot be used as a function  
             d(p); // delete p  
             ~^~~  
In file included from ..\..\../boost/smart_ptr/detail/shared_count.hpp:30:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:28,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of 'void boost::detail::sp_counted_impl_pd<P, D>::dispose() [with P =             boost::detail::thread_data_base*; D = bool]':  
..\..\..\libs\thread\src\win32\thread.cpp:972:1:   required from here  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp:172:9: error: expression cannot be used as a function  
         del( ptr );  
         ^~~  
..\..\..\libs\thread\src\win32\thread.cpp:402:35: warning: 'boost::detail::thread_data_base* boost::{anonymous}::get_or_make_current_thread_            data()' defined but not used [-Wunused-function]  
         detail::thread_data_base* get_or_make_current_thread_data()  
                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:91:14: warning: 'void boost::{anonymous}::cleanup_tls_key()' defined but not used [-Wunused-functi            on]  
         void cleanup_tls_key()  
              ^~~~~~~~~~~~~~~  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -mthreads -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOO            ST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_USE_WINDOW            S_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\t            hread.o" "..\..\..\libs\thread\src\win32\thread.cpp"  
  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi\win32\thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>libboost_thread-gcc64-mt-d-x64-1_67.dll.a             for lack of <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>win32\thread.o...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.exe for lack             of <p..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>libboost_thread-gcc64-mt-d-x64-1_67.dll.a...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.run for lack             of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-win32\threading-multi>test_thread.exe...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\win32\tss_dll.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\win32\tss_pe.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\future.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\compiler_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\win32\thread.o  
..\..\..\libs\thread\src\win32\thread.cpp: In function 'void boost::{anonymous}::create_current_thread_tls_key()':  
..\..\..\libs\thread\src\win32\thread.cpp:84:37: error: 'tss_cleanup_implemented' was not declared in this scope  
             tss_cleanup_implemented(); // if anyone uses TSS, we need the cleanup linked in  
                                     ^  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:322:127: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
         uintptr_t const new_thread=_beginthreadex(0,0,&thread_start_function,thread_info.get(),CREATE_SUSPENDED,&thread_info->id);  
                                                                                                                               ^~  
..\..\..\libs\thread\src\win32\thread.cpp:323:13: error: in argument to unary !  
         if(!new_thread)  
             ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:327:48: error: 'intrusive_ptr_add_ref' was not declared in this scope  
         intrusive_ptr_add_ref(thread_info.get());  
                                                ^  
..\..\..\libs\thread\src\win32\thread.cpp:328:45: error: 'boost::detail::win32' has not been declared  
         thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                             ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::start_thread_noexcept(const attributes&)':  
..\..\..\libs\thread\src\win32\thread.cpp:342:117: error: 'struct boost::detail::thread_data_base' has no member named 'id'  
                                                 CREATE_SUSPENDED | STACK_SIZE_PARAM_IS_A_RESERVATION, &thread_info->id);  
                                                                                                                     ^~  
..\..\..\libs\thread\src\win32\thread.cpp:343:11: error: in argument to unary !  
       if(!new_thread)  
           ^~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:347:46: error: 'intrusive_ptr_add_ref' was not declared in this scope  
       intrusive_ptr_add_ref(thread_info.get());  
                                              ^  
..\..\..\libs\thread\src\win32\thread.cpp:348:43: error: 'boost::detail::win32' has not been declared  
       thread_info->thread_handle=(detail::win32::handle)(new_thread);  
                                           ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In constructor 'boost::{anonymous}::externally_launched_thread::externally_launched_thread()':  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: error: 'count' was not declared in this scope  
                 ++count;  
                   ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:365:19: note: suggested alternative:  
In file included from /usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/algorithm:62:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:39,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/stl_algo.h:3961:5: note:   'std::count'  
     count(_InputIterator __first, _InputIterator __last, const _Tp& __value)  
     ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: error: 'interruption_enabled' was not declared in this scope  
                 interruption_enabled=false;  
                 ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:367:17: note: suggested alternative:  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:592:32: note:   'boost::this_thread::interruption_enabled'  
         bool BOOST_THREAD_DECL interruption_enabled() BOOST_NOEXCEPT;  
                                ^~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:414:16: error: redefinition of 'boost::thread::id boost::thread::get_id() const'  
     thread::id thread::get_id() const BOOST_NOEXCEPT  
                ^~~~~~  
In file included from ..\..\../boost/thread/thread_only.hpp:22:0,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/thread/detail/thread.hpp:716:23: note: 'boost::thread::id boost::thread::get_id() const' previously defined here  
     inline thread::id thread::get_id() const BOOST_NOEXCEPT  
                       ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::join_noexcept()':  
..\..\..\libs\thread\src\win32\thread.cpp:442:13: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
             this_thread::interruptible_wait(this->native_handle(), detail::internal_platform_timepoint::getMax());  
             ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::do_try_join_until_noexcept(const internal_platform_timepo            int&, bool&)':  
..\..\..\libs\thread\src\win32\thread.cpp:457:15: error: 'interruptible_wait' is not a member of 'boost::this_thread'  
           if(!this_thread::interruptible_wait(this->native_handle(), timeout))  
               ^~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'void boost::thread::interrupt()':  
..\..\..\libs\thread\src\win32\thread.cpp:488:32: error: 'struct boost::detail::thread_data_base' has no member named 'interrupt'  
             local_thread_info->interrupt();  
                                ^~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function 'bool boost::thread::interruption_requested() const':  
..\..\..\libs\thread\src\win32\thread.cpp:495:44: error: 'winapi' has not been declared  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                            ^~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:495:93: error: 'struct boost::detail::thread_data_base' has no member named 'interruption_handle';             did you mean 'interrupt_enabled'?  
         return local_thread_info.get() && (winapi::WaitForSingleObjectEx(local_thread_info->interruption_handle,0,0)==0);  
                                                                                             ^~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In static member function 'static unsigned int boost::thread::hardware_concurrency()':  
..\..\..\libs\thread\src\win32\thread.cpp:502:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::system_info info;  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:17: error: 'boost::detail::win32' has not been declared  
         detail::win32::get_system_info(&info);  
                 ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:503:41: error: 'info' was not declared in this scope  
         detail::win32::get_system_info(&info);  
                                         ^~~~  
..\..\..\libs\thread\src\win32\thread.cpp: In member function '__pthread_t* boost::thread::native_handle()':  
..\..\..\libs\thread\src\win32\thread.cpp:541:28: error: 'boost::detail::win32' has not been declared  
             return detail::win32::invalid_handle_value;  
                            ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:25: error: 'boost::detail::win32' has not been declared  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:547:39: error: expected ';' before 'local_thread_info'  
         return (detail::win32::handle)local_thread_info->thread_handle;  
                                       ^~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp: At global scope:  
..\..\..\libs\thread\src\win32\thread.cpp:608:41: error: 'boost::detail::win32' has not been declared  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                         ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:111: error: expected primary-expression before 'const'  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                               ^~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:608:125: error: expression list treated as compound expression in initializer [-fpermissive]  
         bool interruptible_wait(detail::win32::handle handle_to_wait_for, detail::internal_platform_timepoint const &timeout)  
                                                                                                                             ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
 }  
 ^  
..\..\..\libs\thread\src\win32\thread.cpp:972:1: error: expected '}' at end of input  
In file included from ..\..\../boost/smart_ptr/shared_ptr.hpp:28:0,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/shared_count.hpp: In instantiation of 'boost::detail::shared_count::shared_count(P, D) [with P = boost::deta            il::thread_data_base*; D = bool]':  
..\..\../boost/smart_ptr/shared_ptr.hpp:388:76:   required from 'boost::shared_ptr<T>::shared_ptr(Y*, D) [with Y = boost::detail::thread_dat            a_base; D = bool; T = boost::detail::thread_data_base]'  
..\..\..\libs\thread\src\win32\thread.cpp:255:96:   required from here  
..\..\../boost/smart_ptr/detail/shared_count.hpp:185:14: error: 'd' cannot be used as a function  
             d(p); // delete p  
             ~^~~  
In file included from ..\..\../boost/smart_ptr/detail/shared_count.hpp:30:0,  
                 from ..\..\../boost/smart_ptr/shared_ptr.hpp:28,  
                 from ..\..\../boost/shared_ptr.hpp:17,  
                 from ..\..\../boost/date_time/time_clock.hpp:17,  
                 from ..\..\../boost/thread/thread_time.hpp:9,  
                 from ..\..\../boost/thread/lock_types.hpp:18,  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
                 from ..\..\../boost/thread/thread_only.hpp:17,  
                 from ..\..\..\libs\thread\src\win32\thread.cpp:11:  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp: In instantiation of 'void boost::detail::sp_counted_impl_pd<P, D>::dispose() [with P =             boost::detail::thread_data_base*; D = bool]':  
..\..\..\libs\thread\src\win32\thread.cpp:972:1:   required from here  
..\..\../boost/smart_ptr/detail/sp_counted_impl.hpp:172:9: error: expression cannot be used as a function  
         del( ptr );  
         ^~~  
..\..\..\libs\thread\src\win32\thread.cpp:402:35: warning: 'boost::detail::thread_data_base* boost::{anonymous}::get_or_make_current_thread_            data()' defined but not used [-Wunused-function]  
         detail::thread_data_base* get_or_make_current_thread_data()  
                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
..\..\..\libs\thread\src\win32\thread.cpp:91:14: warning: 'void boost::{anonymous}::cleanup_tls_key()' defined but not used [-Wunused-functi            on]  
         void cleanup_tls_key()  
              ^~~~~~~~~~~~~~~  
  
    "g++"   -O0 -fno-inline -Wall -pedantic -g -mthreads -m64 -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOO            ST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_USES_CHRONO -DBOOST_USE_            WINDOWS_H -DWIN32_LEAN_AND_MEAN  -I"..\..\.." -c -o "..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threa            ding-multi\win32\thread.o" "..\..\..\libs\thread\src\win32\thread.cpp"  
  
...failed gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\link-static\threadapi-win32\threading-multi\win32\thread.o..              
  
```

---

## Comment 10

> Username: viboes  
> Created at: 2018-02-20 20:00:43 UTC  
> Updated at: 2018-02-20 20:06:24 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367100963  

Ok, it seems that pthread compiles well, but there is a link issue. It is like if `test_thread.o` was compiled without `BOOST_THREAD_PROVIDES_INTERRUPTIONS` defined. Could you remove evreything in  `..\..\..\bin.v2\libs\thread` and retry again?  
  
With win32 there are a lot of issues.   
  
I see yet   
```  
                 from ..\..\../boost/thread/pthread/thread_data.hpp:12,  
```  
and  
  
```  
            typedef unsigned __int64 ticks_type;  
```  
  
I don't remember which one of pthread or win32 what the default api for cygwin. Since I have no PC Windows I've forgotten this platform a lot, but I believe it should be pthread.  
  
Could you check which int64 type is available on this platform, and replace __int64 wit it?  
Could you provide again the post-processing `-E`?

---

## Comment 11

> Username: viboes  
> Created at: 2018-02-21 01:25:41 UTC  
> Updated at: 2018-02-21 01:29:52 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367181735  

Note that https://github.com/boostorg/thread/pull/211 will remove change the line  
  
```  
            typedef unsigned __int64 ticks_type;  
```  
  
Maybe it is worth concentrating first on `threadapi=pthread`.  
  
Could you add also after  
```  
rule requirements ( properties * )  
{  
    local result ;  
  
    if <threadapi>pthread in $(properties)  
    {  
        result += <define>BOOST_THREAD_POSIX ;  
        if <target-os>windows in $(properties)  
        {  
            local paths = [ win32_pthread_paths $(properties) ] ;  
            if $(paths)  
            {  
                result += $(paths) ;  
            }  
            else  
            {  
                result = <build>no ;  
            }  
        }  
        result += <define>BOOST_THREAD_DONT_USE_CHRONO ;  
        if ! [ configure.builds has_atomic_flag_lockfree   
            : $(properties) : "lockfree boost::atomic_flag" ]  {   
           result += <library>/boost/atomic//boost_atomic ;   
        }   
    } else {  
```  
the following  
  
```  
    if <threadapi>win32 in $(properties)  
    {  
        result += <define>BOOST_THREAD_WIN32 ;  
    }  
```  
  
This could explain the link incoherency.

---

## Comment 12

> Username: Kojoley  
> Created at: 2018-02-21 21:22:19 UTC  
> Updated at: 2018-02-21 21:31:29 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-367476602  

> Could you remove evreything in ..\..\..\bin.v2\libs\thread and retry again?  
  
I've deleted the whole `bin.v2`  
  
<details>  
  <summary>Click to expand: ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread</summary>  
  
```  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$  ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
************************************************************  
Trying to build Boost.Thread with pthread support.  
If you need pthread you should specify the paths.  
You can specify them in site-config.jam, user-config.jam  
or in the environment.  
For example:  
PTW32_INCLUDE=C:\Program Files\ptw32\Pre-built2\include  
PTW32_LIB=C:\Program Files\ptw32\Pre-built2\lib  
************************************************************  
    - lockfree boost::atomic_flag : yes  
...patience...  
...found 2281 targets...  
...updating 65 targets...  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\compiler_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\winrt_init.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\debug.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\decorator.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\execution_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\plain_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\progress_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\results_collector.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\framework.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_framework_init_observer.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\results_reporter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_tools.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_tree.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_log.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_main.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_monitor.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\junit_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\xml_log_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\unit_test_parameters.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\xml_report_formatter.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\auto_timers_construction.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\chrono.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\thread_clock.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\cpu_timer.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\error_code.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\process_cpu_clocks.o  
gcc.link.dll ..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_chrono-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\timer\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_timer-gcc64-mt-d-x64-1_67.dll.a  
gcc.link.dll ..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a  
gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `interruption_point_thread(boost::mutex*, bool*)':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:83: undefined reference to `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:83:(.text+0x1cab): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_thread_interrupts_at_interruption_point()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:93: undefined reference to `boost::thread::interrupt()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:93:(.text+0x1d50): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::interrupt()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `disabled_interruption_point_thread(boost::mutex*, bool*)':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x2452): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:108: undefined reference to `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:108:(.text+0x2457): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::interruption_point()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x246a): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107: undefined reference to `boost::this_thread::disable_interruption::~disable_interruption()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:107:(.text+0x2487): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::this_thread::disable_interruption::~disable_interruption()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_thread_no_interrupt_if_interrupts_disabled_at_interruption_point()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:118: undefined reference to `boost::thread::interrupt()'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:118:(.text+0x2514): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::interrupt()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `do_test_timed_join()':  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:182: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:182:(.text+0x33aa): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:187: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:187:(.text+0x37a5): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:199: undefined reference to `boost::thread::joinable() const'  
/c/Working/Repositories/boost/libs/thread/test/test_thread.cpp:199:(.text+0x3bc0): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `boost::thread::joinable() const'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::interruption_checker::interruption_checker(__pthread_mutex_t**, __pthread_cond_t**)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/thread_data.hpp:199: undefined reference to `boost::detail::get_current_thread_data()'  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/thread_data.hpp:199:(.text$_ZN5boost6detail20interruption_checkerC1EPP17__pthread_mutex_tPP16__pthread_cond_t[_ZN5boost6detail20interruption_checkerC1EPP17__pthread_mutex_tPP16__pthread_cond_t]+0x1b): additional relocation overflows omitted from the output  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::start_thread()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:179: undefined reference to `boost::thread::start_thread_noexcept()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::~thread()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:254: undefined reference to `boost::thread::detach()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::get_id() const':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:719: undefined reference to `boost::thread::native_handle()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::join()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:743: undefined reference to `boost::thread::join_noexcept()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::thread::do_try_join_until(boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:753: undefined reference to `boost::thread::do_try_join_until_noexcept(boost::detail::mono_platform_timepoint const&, bool&)'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::condition_variable::wait(boost::unique_lock<boost::mutex>&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:94: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::condition_variable::do_wait_until(boost::unique_lock<boost::mutex>&, boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:137: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `bool boost::condition_variable_any::do_wait_until<boost::unique_lock<boost::mutex> >(boost::unique_lock<boost::mutex>&, boost::detail::mono_platform_timepoint const&)':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/pthread/condition_variable.hpp:485: undefined reference to `boost::this_thread::interruption_point()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<void (*)()>::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::_bi::bind_t<void, void (*)(boost::thread::id), boost::_bi::list1<boost::_bi::value<boost::thread::id> > > >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::_bi::bind_t<void, void (*)(boost::mutex*, bool*), boost::_bi::list2<boost::_bi::value<boost::mutex*>, boost::_bi::value<bool*> > > >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::reference_wrapper<non_copyable_functor> >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:125: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o: In function `boost::detail::thread_data<boost::reference_wrapper<long_running_thread> >::~thread_data()':  
/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:125: undefined reference to `boost::detail::thread_data_base::~thread_data_base()'  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o:/c/Working/Repositories/boost/libs/thread/test/..\..\../boost/thread/detail/thread.hpp:91: more undefined references to `boost::detail::thread_data_base::~thread_data_base()' follow  
..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o:test_thread.cpp:(.rdata$.refptr._ZTVN5boost6detail16thread_data_baseE[.refptr._ZTVN5boost6detail16thread_data_baseE]+0x0): undefined reference to `vtable for boost::detail::thread_data_base'  
collect2: error: ld returned 1 exit status  
  
    "g++"  -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe" -Wl,--start-group "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o" "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\winrt_init.o" "..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a" "..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -g -mthreads -m64  
  
...failed gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi>test_thread.run for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi>test_thread.exe...  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 62 targets...  
```  
  
</details>  
  
-------------  
  
> Could you provide again the post-processing `-E`?  
  
[test_thread.o.zip](https://github.com/boostorg/thread/files/1745714/test_thread.o.zip)  
  
> Could you check which int64 type is available on this platform, and replace __int64 wit it?  
  
It is not a buildin, but this compiles:  
  
```cpp  
#include <windows.h>  
  
int main()  
{  
    __int64 a = 0;  
    return a;  
}  
```  
  
<details>  
  <summary>Click to expand: int64 in headers</summary>  
  
```  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$ echo | gcc -E -Wp,-v -  
ignoring nonexistent directory "/usr/local/include"  
ignoring nonexistent directory "/usr/lib/gcc/x86_64-pc-msys/6.4.0/../../../../x86_64-pc-msys/include"  
ignoring duplicate directory "/usr/lib/gcc/x86_64-pc-msys/6.4.0/../../../../x86_64-pc-msys/lib/../lib/../../include/w32api"  
#include "..." search starts here:  
#include <...> search starts here:  
 /usr/lib/gcc/x86_64-pc-msys/6.4.0/include  
 /usr/lib/gcc/x86_64-pc-msys/6.4.0/include-fixed  
 /usr/include  
 /usr/lib/../lib/../include/w32api  
End of search list.  
# 1 "<stdin>"  
# 1 "<built-in>"  
# 1 "<command-line>"  
# 1 "<stdin>"  
  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$ grep -r int64 /usr/lib/gcc/x86_64-pc-msys/6.4.0/include  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/postypes.h:#include <stdint.h> // For int64_t  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/bits/postypes.h:  typedef int64_t       streamoff;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t, nano>            nanoseconds;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t, micro>           microseconds;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t, milli>           milliseconds;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t>                  seconds;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t, ratio< 60>>   minutes;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/chrono:    typedef duration<int64_t, ratio<3600>>  hours;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/cstdint:  using ::int64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/cstdint:  using ::uint64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 607, 2,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 1279, 7,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 2281, 12,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 4253, 17,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 11213, 68,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 19937, 122,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 44497, 330,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 86243, 366,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 132049, 110,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random:  typedef simd_fast_mersenne_twister_engine<uint64_t, 216091, 627,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __th = ((static_cast<uint64_t>(___in[3]) << 32)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:                    | static_cast<uint64_t>(___in[2]));  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __tl = ((static_cast<uint64_t>(___in[1]) << 32)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:                    | static_cast<uint64_t>(___in[0]));  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __oh = __th >> (__shift * 8);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __ol = __tl >> (__shift * 8);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __th = ((static_cast<uint64_t>(___in[3]) << 32)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:                    | static_cast<uint64_t>(___in[2]));  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __tl = ((static_cast<uint64_t>(___in[1]) << 32)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:                    | static_cast<uint64_t>(___in[0]));  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __oh = __th << (__shift * 8);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/ext/random.tcc:   uint64_t __ol = __tl << (__shift * 8);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/base.h:      // Better cast down to uint64_t, than up to _DifferenceTp.  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/base.h:      typedef _PseudoSequenceIterator<_Tp, uint64_t> iterator;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/compatibility.h:      int64_t __res;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    uint64_t            _M_supremum;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    uint64_t            _M_rand_sup;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    uint64_t              __cache;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    __scale_down(uint64_t __x,  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:               uint64_t /*_M_supremum*/, double _M_supremum_reciprocal)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:               uint64_t _M_supremum, double /*_M_supremum_reciprocal*/)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    _RandomNumber(uint32_t __seed, uint64_t _M_supremum = 0x100000000ULL)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:    operator()(uint64_t local_supremum)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/random_number.h:          __cache |= ((uint64_t(_M_mt())) << __bits_left);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/types.h:  typedef uint64_t _SequenceIndex;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/parallel/types.h:  typedef int64_t _CASable;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/tr1/cstdint:  using ::int64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/tr1/cstdint:  using ::uint64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/c++config.h:/* Define if int64_t is available in <stdint.h>. */  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/c++config.h:/* Define if int64_t is a long. */  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/c++config.h:/* Define if int64_t is a long long. */  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h: typedef uint64_t __uctype;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h: constexpr uint64_t __maskval = 0xfffffffffffffull;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:                 uint64_t __v1;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:                 uint64_t __v2;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:             uint64_t __v1 = __urng() * __uerngrange + __urng();  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:             uint64_t __v2 = __urng() * __uerngrange + __urng();  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:             uint64_t __v1;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/c++/x86_64-pc-msys/bits/opt_random.h:             uint64_t __v2;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/cilk/cilk_api.h:                            /* Out */    uint64_t *sf_birthrank);  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/cilk/cilk_api.h:int __cilkrts_get_worker_rank(uint64_t *rank)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/cilk/common.h:        typedef __int64 int64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/cilk/common.h:        typedef unsigned __int64 uint64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/cilk/common.h:    uint64_t rank;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/mmintrin.h:_m_from_int64 (long long __i)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/mmintrin.h:_m_to_int64 (__m64 __i)  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/stdint-gcc.h:typedef __INT64_TYPE__ int64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/stdint-gcc.h:typedef __UINT64_TYPE__ uint64_t;  
/usr/lib/gcc/x86_64-pc-msys/6.4.0/include/unwind.h:/* @@@ The IA-64 ABI uses uint64 throughout.  Most places this is  
  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$ grep -r int64 /usr/lib/gcc/x86_64-pc-msys/6.4.0/include-fixed  
  
MSYS /c/Working/Repositories/boost/libs/thread/test  
$ grep -r int64 /usr/include  
/usr/include/alpm.h:#include <stdint.h>    /* int64_t */  
/usr/include/alpm.h:typedef int64_t alpm_time_t;  
/usr/include/bfd.h:typedef BFD_HOST_64_BIT bfd_int64_t;  
/usr/include/bfd.h:typedef BFD_HOST_U_64_BIT bfd_uint64_t;  
/usr/include/bfd.h:typedef bfd_signed_vma bfd_int64_t;  
/usr/include/bfd.h:typedef bfd_vma bfd_uint64_t;  
/usr/include/bfd.h:bfd_uint64_t bfd_getb64 (const void *);  
/usr/include/bfd.h:bfd_uint64_t bfd_getl64 (const void *);  
/usr/include/bfd.h:bfd_int64_t bfd_getb_signed_64 (const void *);  
/usr/include/bfd.h:bfd_int64_t bfd_getl_signed_64 (const void *);  
/usr/include/bfd.h:void bfd_putb64 (bfd_uint64_t, void *);  
/usr/include/bfd.h:void bfd_putl64 (bfd_uint64_t, void *);  
/usr/include/bfd.h:bfd_uint64_t bfd_get_bits (const void *, int, bfd_boolean);  
/usr/include/bfd.h:void bfd_put_bits (bfd_uint64_t, void *, int, bfd_boolean);  
/usr/include/bfd.h:  bfd_uint64_t   (*bfd_getx64) (const void *);  
/usr/include/bfd.h:  bfd_int64_t    (*bfd_getx_signed_64) (const void *);  
/usr/include/bfd.h:  void           (*bfd_putx64) (bfd_uint64_t, void *);  
/usr/include/bfd.h:  bfd_uint64_t   (*bfd_h_getx64) (const void *);  
/usr/include/bfd.h:  bfd_int64_t    (*bfd_h_getx_signed_64) (const void *);  
/usr/include/bfd.h:  void           (*bfd_h_putx64) (bfd_uint64_t, void *);  
/usr/include/cygwin/signal.h:  __uint64_t rip;  
/usr/include/cygwin/signal.h:  __uint64_t rdp;  
/usr/include/cygwin/signal.h:  __uint64_t p1home;  
/usr/include/cygwin/signal.h:  __uint64_t p2home;  
/usr/include/cygwin/signal.h:  __uint64_t p3home;  
/usr/include/cygwin/signal.h:  __uint64_t p4home;  
/usr/include/cygwin/signal.h:  __uint64_t p5home;  
/usr/include/cygwin/signal.h:  __uint64_t p6home;  
/usr/include/cygwin/signal.h:  __uint64_t dr0;  
/usr/include/cygwin/signal.h:  __uint64_t dr1;  
/usr/include/cygwin/signal.h:  __uint64_t dr2;  
/usr/include/cygwin/signal.h:  __uint64_t dr3;  
/usr/include/cygwin/signal.h:  __uint64_t dr6;  
/usr/include/cygwin/signal.h:  __uint64_t dr7;  
/usr/include/cygwin/signal.h:  __uint64_t rax;  
/usr/include/cygwin/signal.h:  __uint64_t rcx;  
/usr/include/cygwin/signal.h:  __uint64_t rdx;  
/usr/include/cygwin/signal.h:  __uint64_t rbx;  
/usr/include/cygwin/signal.h:  __uint64_t rsp;  
/usr/include/cygwin/signal.h:  __uint64_t rbp;  
/usr/include/cygwin/signal.h:  __uint64_t rsi;  
/usr/include/cygwin/signal.h:  __uint64_t rdi;  
/usr/include/cygwin/signal.h:  __uint64_t r8;  
/usr/include/cygwin/signal.h:  __uint64_t r9;  
/usr/include/cygwin/signal.h:  __uint64_t r10;  
/usr/include/cygwin/signal.h:  __uint64_t r11;  
/usr/include/cygwin/signal.h:  __uint64_t r12;  
/usr/include/cygwin/signal.h:  __uint64_t r13;  
/usr/include/cygwin/signal.h:  __uint64_t r14;  
/usr/include/cygwin/signal.h:  __uint64_t r15;  
/usr/include/cygwin/signal.h:  __uint64_t rip;  
/usr/include/cygwin/signal.h:  __uint64_t vregs[52];  
/usr/include/cygwin/signal.h:  __uint64_t vcx;  
/usr/include/cygwin/signal.h:  __uint64_t dbc;  
/usr/include/cygwin/signal.h:  __uint64_t btr;  
/usr/include/cygwin/signal.h:  __uint64_t bfr;  
/usr/include/cygwin/signal.h:  __uint64_t etr;  
/usr/include/cygwin/signal.h:  __uint64_t efr;  
/usr/include/cygwin/signal.h:  __uint64_t oldmask;  
/usr/include/cygwin/signal.h:  __uint64_t cr2;  
/usr/include/cygwin/signal.h:    int64_t _si_pipe_unique_id;  
/usr/include/cygwin/socket.h:#define _SS_ALIGNSIZE (sizeof (int64_t))/* Desired alignment. */  
/usr/include/cygwin/socket.h:  int64_t          __ss_align;  
/usr/include/elf.h:typedef uint64_t Elf32_Xword;  
/usr/include/elf.h:typedef      int64_t  Elf32_Sxword;  
/usr/include/elf.h:typedef uint64_t Elf64_Xword;  
/usr/include/elf.h:typedef      int64_t  Elf64_Sxword;  
/usr/include/elf.h:typedef uint64_t Elf64_Addr;  
/usr/include/elf.h:typedef uint64_t Elf64_Off;  
/usr/include/elf.h:  uint64_t a_type;  
/usr/include/elf.h:      uint64_t a_val;  
/usr/include/fts.h:             int64_t __fts_bignum;  
/usr/include/inttypes.h:#if __int64_t_defined  
/usr/include/machine/endian.h:static __inline __uint64_t  
/usr/include/machine/endian.h:__bswap64(__uint64_t _x)  
/usr/include/machine/endian.h:  return ((__uint64_t)((_x >> 56) | ((_x >> 40) & 0xff00) |  
/usr/include/machine/endian.h:      ((_x << 8) & ((__uint64_t)0xff << 32)) |  
/usr/include/machine/endian.h:      ((_x << 24) & ((__uint64_t)0xff << 40)) |  
/usr/include/machine/endian.h:      ((_x << 40) & ((__uint64_t)0xff << 48)) | ((_x << 56))));  
/usr/include/machine/_default_types.h:typedef __INT64_TYPE__ __int64_t;  
/usr/include/machine/_default_types.h:typedef __UINT64_TYPE__ __uint64_t;  
/usr/include/machine/_default_types.h:typedef unsigned __INT64_TYPE__ __uint64_t;  
/usr/include/machine/_default_types.h:#define ___int64_t_defined 1  
/usr/include/machine/_default_types.h:typedef signed long __int64_t;  
/usr/include/machine/_default_types.h:typedef unsigned long __uint64_t;  
/usr/include/machine/_default_types.h:#define ___int64_t_defined 1  
/usr/include/machine/_default_types.h:typedef signed long long __int64_t;  
/usr/include/machine/_default_types.h:typedef unsigned long long __uint64_t;  
/usr/include/machine/_default_types.h:#define ___int64_t_defined 1  
/usr/include/machine/_default_types.h:typedef signed long long __int64_t;  
/usr/include/machine/_default_types.h:typedef unsigned long long __uint64_t;  
/usr/include/machine/_default_types.h:#define ___int64_t_defined 1  
/usr/include/machine/_default_types.h:typedef signed int __int64_t;  
/usr/include/machine/_default_types.h:typedef unsigned int __uint64_t;  
/usr/include/machine/_default_types.h:#define ___int64_t_defined 1  
/usr/include/machine/_default_types.h:#elif defined(___int64_t_defined)  
/usr/include/machine/_default_types.h:typedef __int64_t __int_least8_t;  
/usr/include/machine/_default_types.h:typedef __uint64_t __uint_least8_t;  
/usr/include/machine/_default_types.h:#elif defined(___int64_t_defined)  
/usr/include/machine/_default_types.h:typedef __int64_t __int_least16_t;  
/usr/include/machine/_default_types.h:typedef __uint64_t __uint_least16_t;  
/usr/include/machine/_default_types.h:#elif defined(___int64_t_defined)  
/usr/include/machine/_default_types.h:typedef __int64_t __int_least32_t;  
/usr/include/machine/_default_types.h:typedef __uint64_t __uint_least32_t;  
/usr/include/machine/_default_types.h:#elif defined(___int64_t_defined)  
/usr/include/machine/_default_types.h:typedef __int64_t __int_least64_t;  
/usr/include/machine/_default_types.h:typedef __uint64_t __uint_least64_t;  
/usr/include/machine/_types.h:typedef __int64_t __blkcnt_t;  
/usr/include/machine/_types.h:typedef __uint64_t __ino_t;  
/usr/include/plugin-api.h:    !defined(UINT64_MAX) && !defined(uint64_t)  
/usr/include/plugin-api.h:#error can not find uint64_t type  
/usr/include/plugin-api.h:  uint64_t size;  
/usr/include/plugin-api.h:    uint64_t segment_flags,  
/usr/include/plugin-api.h:    uint64_t segment_alignment,  
/usr/include/plugin-api.h:                                     uint64_t *secsize);  
/usr/include/python2.7/pyconfig.h:/* Define if your compiler provides int64_t. */  
/usr/include/python2.7/pyconfig.h:/* Define if your compiler provides uint64_t. */  
/usr/include/python2.7/pyconfig.h:/* Define for Solaris 2.5.1 so the uint64_t typedef from <sys/synch.h>,  
/usr/include/python2.7/pyconfig.h:/* #undef int64_t */  
/usr/include/python2.7/pyconfig.h:/* #undef uint64_t */  
/usr/include/python2.7/pyport.h:#ifdef uint64_t  
/usr/include/python2.7/pyport.h:#define PY_UINT64_T uint64_t  
/usr/include/python2.7/pyport.h:#ifdef int64_t  
/usr/include/python2.7/pyport.h:#define PY_INT64_T int64_t  
/usr/include/resolv.h:  int64_t                 __align;        /* 64bit alignment */  
/usr/include/stdint.h:#elif defined(__int64_t_defined)  
/usr/include/stdint.h:#if __int64_t_defined  
/usr/include/sys/acl.h:typedef uint64_t acl_permset_t;  
/usr/include/sys/acl.h:typedef uint64_t acl_entry_t;  
/usr/include/sys/config.h:   __int64_t.  */  
/usr/include/sys/quota.h:    u_int64_t dqb_bhardlimit;  /* absolute limit on disk quota blocks alloc */  
/usr/include/sys/quota.h:    u_int64_t dqb_bsoftlimit;  /* preferred limit on disk quota blocks */  
/usr/include/sys/quota.h:    u_int64_t dqb_curspace;    /* current quota block count */  
/usr/include/sys/quota.h:    u_int64_t dqb_ihardlimit;  /* maximum # allocated inodes */  
/usr/include/sys/quota.h:    u_int64_t dqb_isoftlimit;  /* preferred inode limit */  
/usr/include/sys/quota.h:    u_int64_t dqb_curinodes;   /* current # allocated inodes */  
/usr/include/sys/quota.h:    u_int64_t dqb_btime;       /* time limit for excessive disk use */  
/usr/include/sys/quota.h:    u_int64_t dqb_itime;       /* time limit for excessive files */  
/usr/include/sys/quota.h:    u_int64_t dqi_bgrace;  
/usr/include/sys/quota.h:    u_int64_t dqi_igrace;  
/usr/include/sys/time.h:        uint64_t frac;  
/usr/include/sys/time.h:bintime_addx(struct bintime *_bt, uint64_t _x)  
/usr/include/sys/time.h:        uint64_t _u;  
/usr/include/sys/time.h:        uint64_t _u;  
/usr/include/sys/time.h:        uint64_t _u;  
/usr/include/sys/time.h:        uint64_t _p1, _p2;  
/usr/include/sys/time.h:                _bt->frac |= (uint64_t)_bt->sec << (64 + _exp);  
/usr/include/sys/time.h:        _ts->tv_nsec = ((uint64_t)1000000000 *  
/usr/include/sys/time.h:        _bt->frac = _ts->tv_nsec * (uint64_t)18446744073LL;  
/usr/include/sys/time.h:        _tv->tv_usec = ((uint64_t)1000000 * (uint32_t)(_bt->frac >> 32)) >> 32;  
/usr/include/sys/time.h:        _bt->frac = _tv->tv_usec * (uint64_t)18446744073709LL;  
/usr/include/sys/time.h:        _ts.tv_nsec = ((uint64_t)1000000000 * (uint32_t)_sbt) >> 32;  
/usr/include/sys/time.h:            (_ts.tv_nsec * (((uint64_t)1 << 63) / 500000000) >> 32));  
/usr/include/sys/time.h:        _tv.tv_usec = ((uint64_t)1000000 * (uint32_t)_sbt) >> 32;  
/usr/include/sys/time.h:            (_tv.tv_usec * (((uint64_t)1 << 63) / 500000) >> 32));  
/usr/include/sys/types.h:#if ___int64_t_defined  
/usr/include/sys/types.h:typedef __uint64_t     u_int64_t;  
/usr/include/sys/types.h:#if ___int64_t_defined  
/usr/include/sys/types.h:typedef        __uint64_t      u_quad_t;  
/usr/include/sys/types.h:typedef        __int64_t       quad_t;  
/usr/include/sys/types.h:typedef        __int64_t       sbintime_t;  
/usr/include/sys/_stdint.h:#ifdef ___int64_t_defined  
/usr/include/sys/_stdint.h:typedef __int64_t int64_t ;  
/usr/include/sys/_stdint.h:typedef __uint64_t uint64_t ;  
/usr/include/sys/_stdint.h:#define __int64_t_defined 1  
/usr/include/sys/_stdint.h:#endif /* ___int64_t_defined */  
/usr/include/sys/_types.h:typedef __uint64_t __fsblkcnt_t;  
/usr/include/w32api/authz.h:      PULONG64 pUint64;  
/usr/include/w32api/basetsd.h:__MINGW_EXTENSION typedef unsigned __int64 POINTER_64_INT;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef signed __int64 INT64,*PINT64;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 UINT64,*PUINT64;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef __int64 INT_PTR,*PINT_PTR;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 UINT_PTR,*PUINT_PTR;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef __int64 LONG_PTR,*PLONG_PTR;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 ULONG_PTR,*PULONG_PTR;  
/usr/include/w32api/basetsd.h:#define __int3264 __int64  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef __int64 SHANDLE_PTR;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 HANDLE_PTR;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef __int64 LONG64,*PLONG64;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 ULONG64,*PULONG64;  
/usr/include/w32api/basetsd.h:  __MINGW_EXTENSION typedef unsigned __int64 DWORD64,*PDWORD64;  
/usr/include/w32api/bthsdpdef.h:    LONGLONG              int64;  
/usr/include/w32api/bthsdpdef.h:    ULONGLONG             uint64;  
/usr/include/w32api/cfgmgr32.h:  __MINGW_EXTENSION typedef unsigned __int64 DWORDLONG;  
/usr/include/w32api/cmdtree.h:      __MINGW_EXTENSION __int64 llValue;  
/usr/include/w32api/cmdtree.h:      __MINGW_EXTENSION unsigned __int64 ullValue;  
/usr/include/w32api/comsvcs.h:  __MINGW_EXTENSION typedef unsigned __int64 MTS_OBJID;  
/usr/include/w32api/comsvcs.h:  __MINGW_EXTENSION typedef unsigned __int64 MTS_RESID;  
/usr/include/w32api/comsvcs.h:  __MINGW_EXTENSION typedef unsigned __int64 ULONG64;  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION _variant_t(__int64 i8Src) throw();  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION _variant_t(unsigned __int64 ui8Src) throw();  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION operator __int64() const;  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION operator unsigned __int64() const;  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION _variant_t &operator=(__int64 i8Src);  
/usr/include/w32api/comutil.h:  __MINGW_EXTENSION _variant_t &operator=(unsigned __int64 ui8Src);  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t::_variant_t(__int64 i8Src) throw() {  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t::_variant_t(unsigned __int64 ui8Src) throw() {  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t::operator __int64() const {  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t::operator unsigned __int64() const {  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t &_variant_t::operator=(__int64 i8Src) {  
/usr/include/w32api/comutil.h:__MINGW_EXTENSION inline _variant_t &_variant_t::operator=(unsigned __int64 ui8Src) {  
/usr/include/w32api/correg.h:  __MINGW_EXTENSION typedef unsigned __int64 CLASSVERSION;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef unsigned __int64 size_t;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef __int64 ssize_t;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef __int64 intptr_t;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef unsigned __int64 uintptr_t;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef __int64 ptrdiff_t;  
/usr/include/w32api/crtdefs.h:__MINGW_EXTENSION typedef __int64 __time64_t;  
/usr/include/w32api/ddk/strmini.h:__GNU_EXTENSION typedef unsigned __int64 STREAM_SYSTEM_TIME, *PSTREAM_SYSTEM_TIME;  
/usr/include/w32api/ddk/strmini.h:__GNU_EXTENSION typedef unsigned __int64 STREAM_TIMESTAMP, *PSTREAM_TIMESTAMP;  
/usr/include/w32api/ddk/video.h:  __int64 PhysicalAddress;  
/usr/include/w32api/ddk/wdm.h:#define RtlUlonglongByteSwap(_x) _byteswap_uint64((_x))  
/usr/include/w32api/dmusici.h: * typedef __int64 REFERENCE_TIME,  *LPREFERENCE_TIME;  
/usr/include/w32api/dmusici.h:    STDMETHOD(PlaySegment)(THIS_ IDirectMusicSegment *pSegment, DWORD dwFlags, __int64 i64StartTime, IDirectMusicSegmentState **ppSegmentState) PURE;  
/usr/include/w32api/dmusici.h:    STDMETHOD(PlaySegment)(THIS_ IDirectMusicSegment *pSegment, DWORD dwFlags, __int64 i64StartTime, IDirectMusicSegmentState **ppSegmentState) PURE;  
/usr/include/w32api/dmusici.h:    STDMETHOD(PlaySegmentEx)(THIS_ IUnknown *pSource, WCHAR *pwzSegmentName, IUnknown *pTransition, DWORD dwFlags, __int64 i64StartTime, IDirectMusicSegmentState **ppSegmentState, IUnknown *pFrom, IUnknown *pAudioPath) PURE;  
/usr/include/w32api/dmusici.h:    STDMETHOD(StopEx)(THIS_ IUnknown *pObjectToStop, __int64 i64StopTime, DWORD dwFlags) PURE;  
/usr/include/w32api/esent.h:typedef unsigned __int64 JET_API_PTR;  
/usr/include/w32api/esent.h:  unsigned __int64 cbData;  
/usr/include/w32api/esent.h:  unsigned __int64 cbLongValueData;  
/usr/include/w32api/esent.h:  unsigned __int64 cbOverhead;  
/usr/include/w32api/esent.h:  unsigned __int64 cbLongValueOverhead;  
/usr/include/w32api/esent.h:  unsigned __int64 cNonTaggedColumns;  
/usr/include/w32api/esent.h:  unsigned __int64 cTaggedColumns;  
/usr/include/w32api/esent.h:  unsigned __int64 cLongValues;  
/usr/include/w32api/esent.h:  unsigned __int64 cMultiValues;  
/usr/include/w32api/excpt.h:  __MINGW_EXTENSION _CRTIMP EXCEPTION_DISPOSITION __cdecl __C_specific_handler (struct _EXCEPTION_RECORD *_ExceptionRecord,unsigned __int64 _MemoryStackFp,unsigned __int64 _BackingStoreFp,struct _CONTEXT *_ContextRecord,struct _DISPATCHER_CONTEXT *_DispatcherContext,unsigned __int64 _GlobalPointer);  
/usr/include/w32api/fwptypes.h:        UINT64 *uint64;  
/usr/include/w32api/fwptypes.h:        INT64 *int64;  
/usr/include/w32api/fwptypes.h:        UINT64 *uint64;  
/usr/include/w32api/fwptypes.h:        INT64 *int64;  
/usr/include/w32api/GL/glcorearb.h:/* Define int32_t, int64_t, and uint64_t types for UST/MSC */  
/usr/include/w32api/GL/glcorearb.h:typedef long int int64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef unsigned long int uint64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef __int64 int64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef unsigned __int64 uint64_t;  
/usr/include/w32api/GL/glcorearb.h:typedef uint64_t GLuint64;  
/usr/include/w32api/GL/glcorearb.h:typedef int64_t GLint64;  
/usr/include/w32api/GL/glcorearb.h:typedef GLenum (APIENTRYP PFNGLCLIENTWAITSYNCPROC) (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLWAITSYNCPROC) (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETINTEGER64VPROC) (GLenum pname, GLint64 *data);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETINTEGER64I_VPROC) (GLenum target, GLuint index, GLint64 *data);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETBUFFERPARAMETERI64VPROC) (GLenum target, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLenum APIENTRY glClientWaitSync (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glWaitSync (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetInteger64v (GLenum pname, GLint64 *data);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetInteger64i_v (GLenum target, GLuint index, GLint64 *data);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetBufferParameteri64v (GLenum target, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTI64VPROC) (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTUI64VPROC) (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetQueryObjecti64v (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetQueryObjectui64v (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETINTERNALFORMATI64VPROC) (GLenum target, GLenum internalformat, GLenum pname, GLsizei bufSize, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetInternalformati64v (GLenum target, GLenum internalformat, GLenum pname, GLsizei bufSize, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETTRANSFORMFEEDBACKI64_VPROC) (GLuint xfb, GLenum pname, GLuint index, GLint64 *param);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETNAMEDBUFFERPARAMETERI64VPROC) (GLuint buffer, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETVERTEXARRAYINDEXED64IVPROC) (GLuint vaobj, GLuint index, GLenum pname, GLint64 *param);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetTransformFeedbacki64_v (GLuint xfb, GLenum pname, GLuint index, GLint64 *param);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetNamedBufferParameteri64v (GLuint buffer, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetVertexArrayIndexed64iv (GLuint vaobj, GLuint index, GLenum pname, GLint64 *param);  
/usr/include/w32api/GL/glcorearb.h:typedef uint64_t GLuint64EXT;  
/usr/include/w32api/GL/glcorearb.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTUREHANDLEARBPROC) (GLuint texture);  
/usr/include/w32api/GL/glcorearb.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTURESAMPLERHANDLEARBPROC) (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLENONRESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:typedef GLuint64 (APIENTRYP PFNGLGETIMAGEHANDLEARBPROC) (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLERESIDENTARBPROC) (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLENONRESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64ARBPROC) (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64VARBPROC) (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64ARBPROC) (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64VARBPROC) (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glcorearb.h:typedef GLboolean (APIENTRYP PFNGLISTEXTUREHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:typedef GLboolean (APIENTRYP PFNGLISIMAGEHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64ARBPROC) (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64VARBPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glcorearb.h:typedef void (APIENTRYP PFNGLGETVERTEXATTRIBLUI64VARBPROC) (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLuint64 APIENTRY glGetTextureHandleARB (GLuint texture);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLuint64 APIENTRY glGetTextureSamplerHandleARB (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glMakeTextureHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glMakeTextureHandleNonResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLuint64 APIENTRY glGetImageHandleARB (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glMakeImageHandleResidentARB (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glMakeImageHandleNonResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glUniformHandleui64ARB (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glUniformHandleui64vARB (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glProgramUniformHandleui64ARB (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glProgramUniformHandleui64vARB (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLboolean APIENTRY glIsTextureHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:GLAPI GLboolean APIENTRY glIsImageHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glVertexAttribL1ui64ARB (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glVertexAttribL1ui64vARB (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glcorearb.h:GLAPI void APIENTRY glGetVertexAttribLui64vARB (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:/* Define int32_t, int64_t, and uint64_t types for UST/MSC */  
/usr/include/w32api/GL/glext.h:typedef long int int64_t;  
/usr/include/w32api/GL/glext.h:typedef unsigned long int uint64_t;  
/usr/include/w32api/GL/glext.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glext.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glext.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glext.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glext.h:typedef __int64 int64_t;  
/usr/include/w32api/GL/glext.h:typedef unsigned __int64 uint64_t;  
/usr/include/w32api/GL/glext.h:typedef uint64_t GLuint64;  
/usr/include/w32api/GL/glext.h:typedef int64_t GLint64;  
/usr/include/w32api/GL/glext.h:typedef GLenum (APIENTRYP PFNGLCLIENTWAITSYNCPROC) (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLWAITSYNCPROC) (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETINTEGER64VPROC) (GLenum pname, GLint64 *data);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETINTEGER64I_VPROC) (GLenum target, GLuint index, GLint64 *data);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETBUFFERPARAMETERI64VPROC) (GLenum target, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI GLenum APIENTRY glClientWaitSync (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glWaitSync (GLsync sync, GLbitfield flags, GLuint64 timeout);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetInteger64v (GLenum pname, GLint64 *data);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetInteger64i_v (GLenum target, GLuint index, GLint64 *data);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetBufferParameteri64v (GLenum target, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTI64VPROC) (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTUI64VPROC) (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetQueryObjecti64v (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetQueryObjectui64v (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETINTERNALFORMATI64VPROC) (GLenum target, GLenum internalformat, GLenum pname, GLsizei bufSize, GLint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetInternalformati64v (GLenum target, GLenum internalformat, GLenum pname, GLsizei bufSize, GLint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETTRANSFORMFEEDBACKI64_VPROC) (GLuint xfb, GLenum pname, GLuint index, GLint64 *param);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETNAMEDBUFFERPARAMETERI64VPROC) (GLuint buffer, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVERTEXARRAYINDEXED64IVPROC) (GLuint vaobj, GLuint index, GLenum pname, GLint64 *param);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetTransformFeedbacki64_v (GLuint xfb, GLenum pname, GLuint index, GLint64 *param);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetNamedBufferParameteri64v (GLuint buffer, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVertexArrayIndexed64iv (GLuint vaobj, GLuint index, GLenum pname, GLint64 *param);  
/usr/include/w32api/GL/glext.h:typedef uint64_t GLuint64EXT;  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTUREHANDLEARBPROC) (GLuint texture);  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTURESAMPLERHANDLEARBPROC) (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLENONRESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETIMAGEHANDLEARBPROC) (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLERESIDENTARBPROC) (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLENONRESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64ARBPROC) (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64VARBPROC) (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64ARBPROC) (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64VARBPROC) (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glext.h:typedef GLboolean (APIENTRYP PFNGLISTEXTUREHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef GLboolean (APIENTRYP PFNGLISIMAGEHANDLERESIDENTARBPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64ARBPROC) (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64VARBPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVERTEXATTRIBLUI64VARBPROC) (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetTextureHandleARB (GLuint texture);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetTextureSamplerHandleARB (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeTextureHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeTextureHandleNonResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetImageHandleARB (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeImageHandleResidentARB (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeImageHandleNonResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformHandleui64ARB (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformHandleui64vARB (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformHandleui64ARB (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformHandleui64vARB (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glext.h:GLAPI GLboolean APIENTRY glIsTextureHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI GLboolean APIENTRY glIsImageHandleResidentARB (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1ui64ARB (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1ui64vARB (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVertexAttribLui64vARB (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:#ifndef GL_AMD_gpu_shader_int64  
/usr/include/w32api/GL/glext.h:#define GL_AMD_gpu_shader_int64 1  
/usr/include/w32api/GL/glext.h:typedef int64_t GLint64EXT;  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM1I64NVPROC) (GLint location, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM2I64NVPROC) (GLint location, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM3I64NVPROC) (GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM4I64NVPROC) (GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM1I64VNVPROC) (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM2I64VNVPROC) (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM3I64VNVPROC) (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM4I64VNVPROC) (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM1UI64NVPROC) (GLint location, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM2UI64NVPROC) (GLint location, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM3UI64NVPROC) (GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM4UI64NVPROC) (GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM1UI64VNVPROC) (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM2UI64VNVPROC) (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM3UI64VNVPROC) (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORM4UI64VNVPROC) (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETUNIFORMI64VNVPROC) (GLuint program, GLint location, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETUNIFORMUI64VNVPROC) (GLuint program, GLint location, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM1I64NVPROC) (GLuint program, GLint location, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM2I64NVPROC) (GLuint program, GLint location, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM3I64NVPROC) (GLuint program, GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM4I64NVPROC) (GLuint program, GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM1I64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM2I64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM3I64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM4I64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM1UI64NVPROC) (GLuint program, GLint location, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM2UI64NVPROC) (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM3UI64NVPROC) (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM4UI64NVPROC) (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM1UI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM2UI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM3UI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORM4UI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform1i64NV (GLint location, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform2i64NV (GLint location, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform3i64NV (GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform4i64NV (GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform1i64vNV (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform2i64vNV (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform3i64vNV (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform4i64vNV (GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform1ui64NV (GLint location, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform2ui64NV (GLint location, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform3ui64NV (GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform4ui64NV (GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform1ui64vNV (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform2ui64vNV (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform3ui64vNV (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniform4ui64vNV (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetUniformi64vNV (GLuint program, GLint location, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetUniformui64vNV (GLuint program, GLint location, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform1i64NV (GLuint program, GLint location, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform2i64NV (GLuint program, GLint location, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform3i64NV (GLuint program, GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform4i64NV (GLuint program, GLint location, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform1i64vNV (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform2i64vNV (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform3i64vNV (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform4i64vNV (GLuint program, GLint location, GLsizei count, const GLint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform1ui64NV (GLuint program, GLint location, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform2ui64NV (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform3ui64NV (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform4ui64NV (GLuint program, GLint location, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform1ui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform2ui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform3ui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniform4ui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:#endif /* GL_AMD_gpu_shader_int64 */  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTI64VEXTPROC) (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETQUERYOBJECTUI64VEXTPROC) (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetQueryObjecti64vEXT (GLuint id, GLenum pname, GLint64 *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetQueryObjectui64vEXT (GLuint id, GLenum pname, GLuint64 *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETPERFCOUNTERINFOINTELPROC) (GLuint queryId, GLuint counterId, GLuint counterNameLength, GLchar *counterName, GLuint counterDescLength, GLchar *counterDesc, GLuint *counterOffset, GLuint *counterDataSize, GLuint *counterTypeEnum, GLuint *counterDataTypeEnum, GLuint64 *rawCounterMaxValue);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetPerfCounterInfoINTEL (GLuint queryId, GLuint counterId, GLuint counterNameLength, GLchar *counterName, GLuint counterDescLength, GLchar *counterDesc, GLuint *counterOffset, GLuint *counterDataSize, GLuint *counterTypeEnum, GLuint *counterDataTypeEnum, GLuint64 *rawCounterMaxValue);  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTUREHANDLENVPROC) (GLuint texture);  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETTEXTURESAMPLERHANDLENVPROC) (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLERESIDENTNVPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKETEXTUREHANDLENONRESIDENTNVPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef GLuint64 (APIENTRYP PFNGLGETIMAGEHANDLENVPROC) (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLERESIDENTNVPROC) (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLMAKEIMAGEHANDLENONRESIDENTNVPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64NVPROC) (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMHANDLEUI64VNVPROC) (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64NVPROC) (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMHANDLEUI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glext.h:typedef GLboolean (APIENTRYP PFNGLISTEXTUREHANDLERESIDENTNVPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef GLboolean (APIENTRYP PFNGLISIMAGEHANDLERESIDENTNVPROC) (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetTextureHandleNV (GLuint texture);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetTextureSamplerHandleNV (GLuint texture, GLuint sampler);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeTextureHandleResidentNV (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeTextureHandleNonResidentNV (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI GLuint64 APIENTRY glGetImageHandleNV (GLuint texture, GLint level, GLboolean layered, GLint layer, GLenum format);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeImageHandleResidentNV (GLuint64 handle, GLenum access);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glMakeImageHandleNonResidentNV (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformHandleui64NV (GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformHandleui64vNV (GLint location, GLsizei count, const GLuint64 *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformHandleui64NV (GLuint program, GLint location, GLuint64 value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformHandleui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64 *values);  
/usr/include/w32api/GL/glext.h:GLAPI GLboolean APIENTRY glIsTextureHandleResidentNV (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:GLAPI GLboolean APIENTRY glIsImageHandleResidentNV (GLuint64 handle);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPRESENTFRAMEKEYEDNVPROC) (GLuint video_slot, GLuint64EXT minPresentTime, GLuint beginPresentTimeId, GLuint presentDurationId, GLenum type, GLenum target0, GLuint fill0, GLuint key0, GLenum target1, GLuint fill1, GLuint key1);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPRESENTFRAMEDUALFILLNVPROC) (GLuint video_slot, GLuint64EXT minPresentTime, GLuint beginPresentTimeId, GLuint presentDurationId, GLenum type, GLenum target0, GLuint fill0, GLenum target1, GLuint fill1, GLenum target2, GLuint fill2, GLenum target3, GLuint fill3);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVIDEOI64VNVPROC) (GLuint video_slot, GLenum pname, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVIDEOUI64VNVPROC) (GLuint video_slot, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glPresentFrameKeyedNV (GLuint video_slot, GLuint64EXT minPresentTime, GLuint beginPresentTimeId, GLuint presentDurationId, GLenum type, GLenum target0, GLuint fill0, GLuint key0, GLenum target1, GLuint fill1, GLuint key1);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glPresentFrameDualFillNV (GLuint video_slot, GLuint64EXT minPresentTime, GLuint beginPresentTimeId, GLuint presentDurationId, GLenum type, GLenum target0, GLuint fill0, GLenum target1, GLuint fill1, GLenum target2, GLuint fill2, GLenum target3, GLuint fill3);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVideoi64vNV (GLuint video_slot, GLenum pname, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVideoui64vNV (GLuint video_slot, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:#ifndef GL_NV_shader_atomic_int64  
/usr/include/w32api/GL/glext.h:#define GL_NV_shader_atomic_int64 1  
/usr/include/w32api/GL/glext.h:#endif /* GL_NV_shader_atomic_int64 */  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETBUFFERPARAMETERUI64VNVPROC) (GLenum target, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETNAMEDBUFFERPARAMETERUI64VNVPROC) (GLuint buffer, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETINTEGERUI64VNVPROC) (GLenum value, GLuint64EXT *result);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMUI64NVPROC) (GLint location, GLuint64EXT value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLUNIFORMUI64VNVPROC) (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMUI64NVPROC) (GLuint program, GLint location, GLuint64EXT value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLPROGRAMUNIFORMUI64VNVPROC) (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetBufferParameterui64vNV (GLenum target, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetNamedBufferParameterui64vNV (GLuint buffer, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetIntegerui64vNV (GLenum value, GLuint64EXT *result);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformui64NV (GLint location, GLuint64EXT value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glUniformui64vNV (GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformui64NV (GLuint program, GLint location, GLuint64EXT value);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glProgramUniformui64vNV (GLuint program, GLint location, GLsizei count, const GLuint64EXT *value);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1I64NVPROC) (GLuint index, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL2I64NVPROC) (GLuint index, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL3I64NVPROC) (GLuint index, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL4I64NVPROC) (GLuint index, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1I64VNVPROC) (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL2I64VNVPROC) (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL3I64VNVPROC) (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL4I64VNVPROC) (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64NVPROC) (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL2UI64NVPROC) (GLuint index, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL3UI64NVPROC) (GLuint index, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL4UI64NVPROC) (GLuint index, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL1UI64VNVPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL2UI64VNVPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL3UI64VNVPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLVERTEXATTRIBL4UI64VNVPROC) (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVERTEXATTRIBLI64VNVPROC) (GLuint index, GLenum pname, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETVERTEXATTRIBLUI64VNVPROC) (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1i64NV (GLuint index, GLint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL2i64NV (GLuint index, GLint64EXT x, GLint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL3i64NV (GLuint index, GLint64EXT x, GLint64EXT y, GLint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL4i64NV (GLuint index, GLint64EXT x, GLint64EXT y, GLint64EXT z, GLint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1i64vNV (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL2i64vNV (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL3i64vNV (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL4i64vNV (GLuint index, const GLint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1ui64NV (GLuint index, GLuint64EXT x);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL2ui64NV (GLuint index, GLuint64EXT x, GLuint64EXT y);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL3ui64NV (GLuint index, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL4ui64NV (GLuint index, GLuint64EXT x, GLuint64EXT y, GLuint64EXT z, GLuint64EXT w);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL1ui64vNV (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL2ui64vNV (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL3ui64vNV (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glVertexAttribL4ui64vNV (GLuint index, const GLuint64EXT *v);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVertexAttribLi64vNV (GLuint index, GLenum pname, GLint64EXT *params);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetVertexAttribLui64vNV (GLuint index, GLenum pname, GLuint64EXT *params);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLBUFFERADDRESSRANGENVPROC) (GLenum pname, GLuint index, GLuint64EXT address, GLsizeiptr length);  
/usr/include/w32api/GL/glext.h:typedef void (APIENTRYP PFNGLGETINTEGERUI64I_VNVPROC) (GLenum value, GLuint index, GLuint64EXT *result);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glBufferAddressRangeNV (GLenum pname, GLuint index, GLuint64EXT address, GLsizeiptr length);  
/usr/include/w32api/GL/glext.h:GLAPI void APIENTRY glGetIntegerui64i_vNV (GLenum value, GLuint index, GLuint64EXT *result);  
/usr/include/w32api/GL/glext.h:typedef GLenum (APIENTRYP PFNGLVIDEOCAPTURENVPROC) (GLuint video_capture_slot, GLuint *sequence_num, GLuint64EXT *capture_time);  
/usr/include/w32api/GL/glext.h:GLAPI GLenum APIENTRY glVideoCaptureNV (GLuint video_capture_slot, GLuint *sequence_num, GLuint64EXT *capture_time);  
/usr/include/w32api/GL/glxext.h:/* Define int32_t, int64_t, and uint64_t types for UST/MSC */  
/usr/include/w32api/GL/glxext.h:typedef long int int64_t;  
/usr/include/w32api/GL/glxext.h:typedef unsigned long int uint64_t;  
/usr/include/w32api/GL/glxext.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glxext.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glxext.h:typedef long long int int64_t;  
/usr/include/w32api/GL/glxext.h:typedef unsigned long long int uint64_t;  
/usr/include/w32api/GL/glxext.h:typedef __int64 int64_t;  
/usr/include/w32api/GL/glxext.h:typedef unsigned __int64 uint64_t;  
/usr/include/w32api/GL/glxext.h:typedef Bool ( *PFNGLXGETSYNCVALUESOMLPROC) (Display *dpy, GLXDrawable drawable, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/GL/glxext.h:typedef int64_t ( *PFNGLXSWAPBUFFERSMSCOMLPROC) (Display *dpy, GLXDrawable drawable, int64_t target_msc, int64_t divisor, int64_t remainder);  
/usr/include/w32api/GL/glxext.h:typedef Bool ( *PFNGLXWAITFORMSCOMLPROC) (Display *dpy, GLXDrawable drawable, int64_t target_msc, int64_t divisor, int64_t remainder, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/GL/glxext.h:typedef Bool ( *PFNGLXWAITFORSBCOMLPROC) (Display *dpy, GLXDrawable drawable, int64_t target_sbc, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/GL/glxext.h:Bool glXGetSyncValuesOML (Display *dpy, GLXDrawable drawable, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/GL/glxext.h:int64_t glXSwapBuffersMscOML (Display *dpy, GLXDrawable drawable, int64_t target_msc, int64_t divisor, int64_t remainder);  
/usr/include/w32api/GL/glxext.h:Bool glXWaitForMscOML (Display *dpy, GLXDrawable drawable, int64_t target_msc, int64_t divisor, int64_t remainder, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/GL/glxext.h:Bool glXWaitForSbcOML (Display *dpy, GLXDrawable drawable, int64_t target_sbc, int64_t *ust, int64_t *msc, int64_t *sbc);  
/usr/include/w32api/httpfilt.h:  __MINGW_EXTENSION typedef unsigned __int64 ULONG_PTR,*PULONG_PTR;  
/usr/include/w32api/intrin.h:  __MACHINEIA64(__MINGW_EXTENSION void _AcquireSpinLock(unsigned __int64 *))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION __int64 __cdecl _abs64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINE(__MINGW_EXTENSION unsigned __int64 __cdecl _byteswap_uint64(unsigned __int64 value))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION double _CopyDoubleFromInt64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION __int64 _CopyInt64FromDouble(double))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION unsigned _CountLeadingOnes64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION unsigned _CountLeadingSigns64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION unsigned _CountLeadingZeros64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINECE(__MINGW_EXTENSION unsigned _CountOneBits64(__int64))  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION __int64 __emul(int,int))  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION unsigned __int64 __emulu(unsigned int,unsigned int))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __fc(__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION unsigned __int64 __getReg(int))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION unsigned __int64 __getPSP(void))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION unsigned __int64 __getCFS(void))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedAdd64(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedAdd64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedAdd64_rel(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedDecrement64(__int64 volatile *))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedDecrement64_acq(__int64 volatile *))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedDecrement64_rel(__int64 volatile *))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedDecrement64(__int64 volatile *)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedExchange64(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedExchange64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedExchange64(__int64 volatile *,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64_rel(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64(__int64 volatile *,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompareExchange64(__int64 volatile *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompareExchange64_acq(__int64 volatile *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompareExchange64_rel(__int64 volatile *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_acq(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_rel(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_acq(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_rel(__int64 volatile *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    /* __MACHINEI(__MINGW_EXTENSION __int64 _InterlockedCompareExchange64(__int64 volatile *,__int64,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedIncrement64(__int64 volatile *))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedIncrement64_acq(__int64 volatile *))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedIncrement64_rel(__int64 volatile *))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedIncrement64(__int64 volatile *)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION __int64 _InterlockedOr64(__int64 volatile *,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedOr64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedOr64_rel(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION __int64 _InterlockedXor64(__int64 volatile *,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedXor64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedXor64_rel(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION __int64 _InterlockedAnd64(__int64 volatile *,__int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedAnd64_acq(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 _InterlockedAnd64_rel(__int64 volatile *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA32(__MINGW_EXTENSION __LONG32 _InterlockedAddLargeStatistic(__int64 volatile *,__LONG32))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 __load128(void *,__int64 *))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 __load128_acq(void *,__int64 *))  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION unsigned __int64 __ll_lshift(unsigned __int64,int))  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION __int64 __ll_rshift(__int64,int))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __ptcl(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __ptcg(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __ptcga(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __ptrd(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __ptri(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void _ReleaseSpinLock(unsigned __int64 *))  
/usr/include/w32api/intrin.h:    __MACHINE(__MINGW_EXTENSION unsigned __int64 __cdecl _rotl64(unsigned __int64,int))  
/usr/include/w32api/intrin.h:    __MACHINE(__MINGW_EXTENSION unsigned __int64 __cdecl _rotr64(unsigned __int64,int))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __setReg(int,unsigned __int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __store128(void *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION void __store128_rel(void *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 __thash(__int64))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION __int64 __ttag(__int64))  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION unsigned __int64 __ull_rshift(unsigned __int64,int))  
/usr/include/w32api/intrin.h:    __MACHINEIA64(__MINGW_EXTENSION unsigned __int64 __UMULH(unsigned __int64 a,unsigned __int64 b))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pextrw(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 __mulh(__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __umulh(unsigned __int64,unsigned __int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readcr0(void)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readcr2(void)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readcr3(void)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readcr4(void)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readcr8(void)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __writecr0(unsigned __int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __writecr3(unsigned __int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __writecr4(unsigned __int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __writecr8(unsigned __int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEI(__MINGW_EXTENSION unsigned __int64 __readmsr(unsigned __LONG32)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEI(__MINGW_EXTENSION void __writemsr(unsigned __LONG32,unsigned __int64)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEIW64(__MINGW_EXTENSION unsigned __int64 __rdtsc(void))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned __int64 __readgsqword(unsigned __LONG32 Offset)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __writegsqword(unsigned __LONG32 Offset,unsigned __int64 Data)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __m128i _mm_set_epi64x(__int64 i1,__int64 i0))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __m128i _mm_set1_epi64x(__int64 i))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _mm_cvtsd_si64x(__m128d a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __m128d _mm_cvtsi64x_sd(__m128d a,__int64 b))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __m128 _mm_cvtsi64x_ss(__m128 a,__int64 b))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _mm_cvtss_si64x(__m128 a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _mm_cvttsd_si64x(__m128d a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _mm_cvttss_si64x(__m128 a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __m128i _mm_cvtsi64x_si128(__int64 a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _mm_cvtsi128_si64x(__m128i a))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION void _mm_stream_si64x(__int64 *,__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION void __stosq(unsigned __int64 *,unsigned __int64,size_t)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _bittest64(__int64 const *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _bittestandset64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _bittestandreset64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _bittestandcomplement64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char InterlockedBitTestAndSet64(volatile __int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char InterlockedBitTestAndReset64(volatile __int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char InterlockedBitTestAndComplement64(volatile __int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char _interlockedbittestandset64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char _interlockedbittestandreset64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEX64(__MINGW_EXTENSION unsigned char _interlockedbittestandcomplement64(__int64 *a,__int64 b)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINEI(__MINGW_EXTENSION unsigned __int64 __readpmc(unsigned __LONG32 a))  
/usr/include/w32api/intrin.h:    /* __MACHINEIA32(__MINGW_EXTENSION unsigned __int64 __readfsqword(unsigned __LONG32 Offset)) intrinsic doesn't actually exist */  
/usr/include/w32api/intrin.h:    /* __MACHINEIA32(__MINGW_EXTENSION void __writefsqword(unsigned __LONG32 Offset,unsigned __int64 Data)) intrinsic doesn't actually exist */  
/usr/include/w32api/intrin.h:    __MACHINE(__MINGW_EXTENSION __int64 __cdecl _abs64(__int64))  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _BitScanForward64(unsigned __LONG32 *Index,unsigned __int64 Mask)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned char _BitScanReverse64(unsigned __LONG32 *Index,unsigned __int64 Mask)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned __int64 __shiftleft128(unsigned __int64 LowPart,unsigned __int64 HighPart,unsigned char Shift)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned __int64 __shiftright128(unsigned __int64 LowPart,unsigned __int64 HighPart,unsigned char Shift)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION unsigned __int64 _umul128(unsigned __int64 multiplier,unsigned __int64 multiplicand,unsigned __int64 *highproduct)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    /* __MACHINEW64(__MINGW_EXTENSION __int64 _mul128(__int64 multiplier,__int64 multiplicand,__int64 *highproduct)) moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddsb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddsw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddsd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddusb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddusw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paddusd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubsb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubsw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubsd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubusb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubusw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psubusd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaddwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmadduwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmulhw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmulhuw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmullw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmullw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmacsw(unsigned __int64 m1,unsigned __int64 m2,unsigned __int64 m3))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmacuw(unsigned __int64 m1,unsigned __int64 m2,unsigned __int64 m3))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmacszw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_padduzw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paccb(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paccw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paccd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmia(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmiaph(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmiabb(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmiabt(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmiatb(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmiatt(unsigned __int64 m1,int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psllw(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psllwi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pslld(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pslldi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psllq(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psllqi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psraw(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrawi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrad(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psradi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psraq(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psraqi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrlw(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrlwi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrld(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrldi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrlq(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psrlqi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prorw(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prorwi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prord(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prordi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prorq(unsigned __int64 m1,unsigned __int64 count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_prorqi(unsigned __int64 m1,int count))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pand(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pandn(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_por(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pxor(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpeqb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpeqw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpeqd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtub(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtuw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pcmpgtud(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packsswb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packssdw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packssqd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packuswb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packusdw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_packusqd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckhbw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckhwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckhdq(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpcklbw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpcklwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckldq(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehsbw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehswd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehsdq(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehubw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehuwd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckehudq(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckelsbw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckelswd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckelsdq(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckelubw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckeluwd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_punpckeludq(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_setzero_si64())  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set_pi32(int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set_pi16(short s3,short s2,short s1,short s0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set_pi8(char b7,char b6,char b5,char b4,char b3,char b2,char b1,char b0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set1_pi32(int i))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set1_pi16(short s))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_set1_pi8(char b))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_setr_pi32(int i1,int i0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_setr_pi16(short s3,short s2,short s1,short s0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _mm_setr_pi8(char b7,char b6,char b5,char b4,char b3,char b2,char b1,char b0))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pextrb(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pextrd(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned int _m_pextrub(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned int _m_pextruw(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned int _m_pextrud(unsigned __int64 m1,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pinsrb(unsigned __int64 m1,int i,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pinsrw(unsigned __int64 m1,int i,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pinsrd(unsigned __int64 m1,int i,const int c))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxsb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxsw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxsd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxub(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxuw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pmaxud(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminsb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminsw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminsd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminub(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminuw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pminud(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pmovmskb(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pmovmskw(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION int _m_pmovmskd(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pshufw(unsigned __int64 m1,int i))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pavgb(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pavgw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pavg2b(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_pavg2w(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psadbw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psadwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psadzbw(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_psadzwd(unsigned __int64 m1,unsigned __int64 m2))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_paligniq(unsigned __int64 m1,unsigned __int64 m2,int i))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION unsigned __int64 _m_cvt_si2pi(__int64 i))  
/usr/include/w32api/intrin.h:    __MACHINECC(__MINGW_EXTENSION __int64 _m_cvt_pi2si(unsigned __int64 m1))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompareExchange64_np(__int64 *,__int64,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_np(__int64 *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_acq_np(__int64 *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedCompare64Exchange128_rel_np(__int64 *Destination,__int64 ExchangeHigh,__int64 ExchangeLow,__int64 Comparand))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedAnd64_np(__int64 *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedOr64_np(__int64 *,__int64))  
/usr/include/w32api/intrin.h:    __MACHINEX64(__MINGW_EXTENSION __int64 _InterlockedXor64_np(__int64 *,__int64))  
/usr/include/w32api/mapidefs.h:    LONGLONG int64;  
/usr/include/w32api/mfapi.h:  typedef unsigned __int64 MFWORKITEM_KEY;  
/usr/include/w32api/mlang.h:  __MINGW_EXTENSION typedef __int64 SCRIPT_IDS;  
/usr/include/w32api/msrdc.h:  unsigned __int64 m_FileOffset;  
/usr/include/w32api/msrdc.h:  unsigned __int64 m_BlockLength;  
/usr/include/w32api/msrdc.h:    STDMETHOD_(HRESULT,Get)(THIS_ unsigned __int64 fileOffset,WINBOOL dirty,DWORD numElements,SimilarityMappedViewInfo *viewInfo) PURE;  
/usr/include/w32api/msrdc.h:    STDMETHOD_(HRESULT,SetFileSize)(THIS_ unsigned __int64 *fileSize) PURE;  
/usr/include/w32api/msrdc.h:    STDMETHOD_(HRESULT,GetFileSize)(THIS_ unsigned __int64 *fileSize) PURE;  
/usr/include/w32api/msrdc.h:    STDMETHOD_(HRESULT,OpenMapping)(THIS_ RdcMappingAccessMode accessMode,unsigned __int64 begin,unsigned __int64 end,unsigned __int64 *actualEnd) PURE;  
/usr/include/w32api/msrdc.h:    STDMETHOD_(HRESULT,ResizeMapping)(THIS_ RdcMappingAccessMode accessMode,unsigned __int64 begin,unsigned __int64 end,unsigned __int64 *actualEnd) PURE;  
/usr/include/w32api/ndr64types.h:__MINGW_EXTENSION typedef unsigned __int64 NDR64_UINT64;  
/usr/include/w32api/ndr64types.h:__MINGW_EXTENSION typedef __int64 NDR64_INT64;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeElapsed;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 MacBytesReceivedEx;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:    __MINGW_EXTENSION __int64 TimeStamp;  
/usr/include/w32api/netmon.h:  __MINGW_EXTENSION __int64 WINAPI GetFrameTimeStamp(HFRAME hFrame);  
/usr/include/w32api/netmon.h:  __MINGW_EXTENSION HFRAME WINAPI ModifyFrame(HCAPTURE hCapture,DWORD FrameNumber,LPBYTE FrameData,DWORD FrameLength,__int64 TimeStamp);  
/usr/include/w32api/netmon.h:  __MINGW_EXTENSION SYSTEMTIME *WINAPI AdjustSystemTime(SYSTEMTIME *SystemTime,__int64 TimeDelta);  
/usr/include/w32api/newapis.h:      *(__int64 *)pliQuota = Int32x32To64(dwBytesPerClus,dwFreeClus);  
/usr/include/w32api/newapis.h:      *(__int64 *)pliTotal = Int32x32To64(dwBytesPerClus,dwTotalClus);  
/usr/include/w32api/ntdef.h:__MINGW_EXTENSION typedef __int64 LONGLONG, *PLONGLONG;  
/usr/include/w32api/ntdef.h:__MINGW_EXTENSION typedef unsigned __int64 ULONGLONG, *PULONGLONG;  
/usr/include/w32api/ntdef.h:    __MINGW_EXTENSION __int64 UseThisFieldToCopy;  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: char, short, __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: char, short, __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: unsigned __LONG32 or unsigned __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: __LONG32 or __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: unsigned __LONG32, unsigned __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: unsigned __LONG32, unsigned __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:   DataType: unsigned char, unsigned short, unsigned __LONG32, unsigned __int64  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __stosq(unsigned __int64 *, unsigned __int64, size_t);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildstos(__stosq, unsigned __int64, "q|q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _interlockedbittestandset64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(_interlockedbittestandset64, __int64, "lock bts{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _interlockedbittestandreset64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(_interlockedbittestandreset64, __int64, "lock btr{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _interlockedbittestandcomplement64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(_interlockedbittestandcomplement64, __int64, "lock btc{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char InterlockedBitTestAndSet64(volatile __int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(InterlockedBitTestAndSet64, volatile __int64, "lock bts{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char InterlockedBitTestAndReset64(volatile __int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(InterlockedBitTestAndReset64, volatile __int64, "lock btr{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char InterlockedBitTestAndComplement64(volatile __int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittesti(InterlockedBitTestAndComplement64, volatile __int64, "lock btc{q %[Offset],%[Base] | %[Base],%[Offset]}" __FLAGSET, "J", __int64)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedAnd64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedAnd64, __int64, and)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedOr64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedOr64, __int64, or)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedXor64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedXor64, __int64, xor)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedIncrement64(__int64 volatile *Addend);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedIncrement64(__int64 volatile *Addend) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedDecrement64(__int64 volatile *Addend);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedDecrement64(__int64 volatile *Addend) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedExchange64(__int64 volatile *Target, __int64 Value);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedExchange64(__int64 volatile *Target, __int64 Value) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64(__int64 volatile *Addend, __int64 Value);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedExchangeAdd64(__int64 volatile *Addend, __int64 Value) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readgsqword(unsigned __LONG32 Offset);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildreadseg(__readgsqword, unsigned __int64, "gs", "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writegsqword(unsigned __LONG32 Offset,unsigned __int64 Data);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildwriteseg(__writegsqword, unsigned __int64, "gs", "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _BitScanForward64(unsigned __LONG32 *Index, unsigned __int64 Mask);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbitscan(_BitScanForward64, unsigned __int64, "bsf{q %[Mask],%[Index] | %[Index],%[Mask]}")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _BitScanReverse64(unsigned __LONG32 *Index, unsigned __int64 Mask);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbitscan(_BitScanReverse64, unsigned __int64, "bsr{q %[Mask],%[Index] | %[Index],%[Mask]}")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _bittest64(__int64 const *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittest(_bittest64, __int64, "q", "J")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _bittestandset64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittestand(_bittestandset64, __int64, "bts", "J", "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _bittestandreset64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittestand(_bittestandreset64, __int64, "btr", "J", "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned char _bittestandcomplement64(__int64 *a, __int64 b);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildbittestand(_bittestandcomplement64, __int64, "btc", "J", "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readcr0(void);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_readcr(__readcr0, unsigned __int64, "0")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readcr2(void);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_readcr(__readcr2, unsigned __int64, "2")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readcr3(void);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_readcr(__readcr3, unsigned __int64, "3")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readcr4(void);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_readcr(__readcr4, unsigned __int64, "4")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readcr8(void);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_readcr(__readcr8, unsigned __int64, "8")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writecr0(unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_writecr(__writecr0, unsigned __int64, "0")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writecr3(unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_writecr(__writecr3, unsigned __int64, "3")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writecr4(unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_writecr(__writecr4, unsigned __int64, "4")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writecr8(unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__build_writecr(__writecr8, unsigned __int64, "8")  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __movsq(unsigned __int64 *Dest, unsigned __int64 const *Source, size_t Count);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildmov(__movsq, unsigned __int64, "q")  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 _umul128(unsigned __int64, unsigned __int64, unsigned __int64 *);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 _umul128(unsigned __int64 a, unsigned __int64 b, unsigned __int64 *hi)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   __MINGW_EXTENSION union { unsigned __int128 v; unsigned __int64 sv[2]; } var;  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _mul128(__int64, __int64, __int64 *);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _mul128(__int64 a, __int64 b, __int64 *hi)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   __MINGW_EXTENSION union { __int128 v; __int64 sv[2]; } var;  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __shiftleft128(unsigned __int64  LowPart, unsigned __int64 HighPart, unsigned char Shift);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __shiftleft128 (unsigned __int64  LowPart, unsigned __int64 HighPart, unsigned char Shift)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   unsigned __int64 ret;  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __shiftright128 (unsigned __int64  LowPart, unsigned __int64 HighPart, unsigned char Shift);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __shiftright128 (unsigned __int64  LowPart, unsigned __int64 HighPart, unsigned char Shift)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   unsigned __int64 ret;  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedAnd64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedAnd64, __int64, and)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedOr64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedOr64, __int64, or)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedXor64(__int64 volatile *, __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__buildlogicali(_InterlockedXor64, __int64, xor)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedIncrement64(__int64 volatile *Addend);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedIncrement64(__int64 volatile *Addend) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedDecrement64(__int64 volatile *Addend);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedDecrement64(__int64 volatile *Addend) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedExchange64(__int64 volatile *Target, __int64 Value);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedExchange64(__int64 volatile *Target, __int64 Value) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedExchangeAdd64(__int64 volatile *Addend, __int64 Value);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedExchangeAdd64(__int64 volatile *Addend, __int64 Value) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __popcnt64(unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __popcnt64(unsigned __int64 value)  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedAdd64(__int64 volatile *Addend, __int64 Value);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedAdd64(__int64 volatile *Addend, __int64 Value) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION __int64 _InterlockedCompareExchange64(__int64 volatile *Destination, __int64 ExChange, __int64 Comperand);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__int64 _InterlockedCompareExchange64(__int64 volatile *Destination, __int64 ExChange, __int64 Comperand) {  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION unsigned __int64 __readmsr(unsigned __LONG32);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 __readmsr(unsigned __LONG32 msr)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   unsigned __int64 val1, val2;  
/usr/include/w32api/psdk_inc/intrin-impl.h:   return ((unsigned __int64) val1) | (((unsigned __int64)val2) << 32);  
/usr/include/w32api/psdk_inc/intrin-impl.h:__MINGW_EXTENSION void __writemsr(unsigned __LONG32, unsigned __int64);  
/usr/include/w32api/psdk_inc/intrin-impl.h:void __writemsr(unsigned __LONG32 msr, unsigned __int64 Value)  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 _xgetbv(unsigned int);  
/usr/include/w32api/psdk_inc/intrin-impl.h:unsigned __int64 _xgetbv(unsigned int index)  
/usr/include/w32api/psdk_inc/intrin-impl.h:   unsigned __int64 val1, val2;  
/usr/include/w32api/psdk_inc/intrin-impl.h:   return (((unsigned __int64)val2) << 32) | val1;  
/usr/include/w32api/rpcndr.h:#define hyper /* __MINGW_EXTENSION */ __int64  
/usr/include/w32api/rpcndr.h:#define MIDL_uhyper /* __MINGW_EXTENSION */ unsigned __int64  
/usr/include/w32api/rpcndr.h:  __MINGW_EXTENSION typedef unsigned __int64 size_t;  
/usr/include/w32api/rpcndr.h:  __MINGW_EXTENSION typedef __int64 ssize_t;  
/usr/include/w32api/rtcapi.h:  char __fastcall _RTC_Check_8_to_1(__int64 _Src);  
/usr/include/w32api/rtcapi.h:  short __fastcall _RTC_Check_8_to_2(__int64 _Src);  
/usr/include/w32api/rtcapi.h:  int __fastcall _RTC_Check_8_to_4(__int64 _Src);  
/usr/include/w32api/shellapi.h:    __MINGW_EXTENSION __int64 i64Size;  
/usr/include/w32api/shellapi.h:    __MINGW_EXTENSION __int64 i64NumItems;  
/usr/include/w32api/sqltypes.h:#define ODBCINT64 /* __MINGW_EXTENSION */ __int64  
/usr/include/w32api/sspi.h:  __MINGW_EXTENSION typedef unsigned __int64 QWORD;  
/usr/include/w32api/strsafe.h:__MINGW_EXTENSION typedef unsigned __int64 size_t;  
/usr/include/w32api/strsafe.h:__MINGW_EXTENSION typedef __int64 ssize_t;  
/usr/include/w32api/timeprov.h:    __MINGW_EXTENSION signed __int64 toOffset;  
/usr/include/w32api/timeprov.h:    __MINGW_EXTENSION signed __int64 toDelay;  
/usr/include/w32api/timeprov.h:    __MINGW_EXTENSION unsigned __int64 tpDispersion;  
/usr/include/w32api/timeprov.h:    __MINGW_EXTENSION unsigned __int64 nSysTickCount;  
/usr/include/w32api/timeprov.h:    __MINGW_EXTENSION signed __int64 nSysPhaseOffset;  
/usr/include/w32api/wabdefs.h:    LONGLONG int64;  
/usr/include/w32api/wbemdisp.h:    wbemCimtypeSint64 = 20,  
/usr/include/w32api/wbemdisp.h:    wbemCimtypeUint64 = 21,  
/usr/include/w32api/winbase.h:    return (unsigned long) InterlockedIncrement64 ((volatile __int64 *) Addend);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedIncrement (unsigned __int64 volatile *Addend) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedIncrement64 ((volatile __int64 *) Addend);  
/usr/include/w32api/winbase.h:    return (unsigned long) InterlockedDecrement64 ((volatile __int64 *) Addend);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedDecrement (unsigned __int64 volatile *Addend) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedDecrement64 ((volatile __int64 *) Addend);  
/usr/include/w32api/winbase.h:    return (unsigned long) InterlockedExchange64 ((volatile __int64 *) Target,(__int64) Value);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedExchange (unsigned __int64 volatile *Target, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedExchange64 ((volatile __int64 *) Target,(__int64) Value);  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedExchangeAdd64 ((volatile __int64 *) Addend,(__int64) Value);  
/usr/include/w32api/winbase.h:    return (unsigned long) InterlockedExchangeAdd64 ((volatile __int64 *) Addend,- (__int64) Value);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedExchangeAdd (unsigned __int64 volatile *Addend, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedExchangeAdd64 ((volatile __int64 *) Addend,(__int64) Value);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedExchangeSubtract (unsigned __int64 volatile *Addend, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedExchangeAdd64 ((volatile __int64 *) Addend,- (__int64) Value);  
/usr/include/w32api/winbase.h:    return (unsigned long) InterlockedCompareExchange64 ((volatile __int64 *) Destination,(__int64) Exchange,(__int64) Comperand);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedCompareExchange (unsigned __int64 volatile *Destination, unsigned __int64 Exchange, unsigned __int64 Comperand) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedCompareExchange64 ((volatile __int64 *) Destination,(__int64) Exchange,(__int64) Comperand);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedAnd (unsigned __int64 volatile *Destination, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedAnd64 ((volatile __int64 *) Destination,(__int64) Value);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedOr (unsigned __int64 volatile *Destination, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedOr64 ((volatile __int64 *) Destination,(__int64) Value);  
/usr/include/w32api/winbase.h:  FORCEINLINE unsigned __int64 InterlockedXor (unsigned __int64 volatile *Destination, unsigned __int64 Value) {  
/usr/include/w32api/winbase.h:    return (unsigned __int64) InterlockedXor64 ((volatile __int64 *) Destination,(__int64) Value);  
/usr/include/w32api/windns.h:  __MINGW_EXTENSION typedef unsigned __int64 QWORD,*PQWORD;  
/usr/include/w32api/winnt.h:      __MINGW_EXTENSION __int64 LowPart;  
/usr/include/w32api/winnt.h:      __MINGW_EXTENSION __int64 HighPart;  
/usr/include/w32api/winnt.h:  __MINGW_EXTENSION typedef __int64 LONGLONG;  
/usr/include/w32api/winnt.h:  __MINGW_EXTENSION typedef unsigned __int64 ULONGLONG;  
/usr/include/w32api/winnt.h:    __MINGW_EXTENSION unsigned __int64 __cdecl _rotl64(unsigned __int64 Value,int Shift);  
/usr/include/w32api/winnt.h:    __MINGW_EXTENSION unsigned __int64 __cdecl _rotr64(unsigned __int64 Value,int Shift);  
/usr/include/w32api/winnt.h:    /* __MINGW_EXTENSION unsigned __int64 __readgsqword(unsigned __LONG32 Offset); moved to psdk_inc/intrin-impl.h */  
/usr/include/w32api/winnt.h:    PDWORD64 pUint64;  
/usr/include/w32api/winnt.h:    DWORD pUint64[ANYSIZE_ARRAY];  
/usr/include/w32api/winsock2.h:    __MINGW_EXTENSION __int64 __ss_align;  
/usr/include/w32api/wmiatlprov.h:      __MINGW_EXTENSION unsigned __int64 ullPathInfo;  
/usr/include/w32api/wmiutils.h:    __MINGW_EXTENSION __int64 m_lVal64;  
/usr/include/w32api/wmiutils.h:    __MINGW_EXTENSION __int64 m_uVal64;  
/usr/include/w32api/wmiutils.h:    __MINGW_EXTENSION unsigned __int64 m_uParsedFeatureMask;  
/usr/include/w32api/wtypes.h:    LONGLONG int64;  
/usr/include/w32api/wtypes.h:  LONGLONG int64;  
/usr/include/w32api/_bsd_types.h:typedef unsigned long long u_int64;  
/usr/include/w32api/_mingw.h:#define __int64 long long  
  
```  
  
</details>  
  
IIUC it is a define `/usr/include/w32api/_mingw.h:#define __int64 long long`

---

## Comment 13

> Username: viboes  
> Created at: 2018-02-26 15:46:57 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-368546423  

Have you added twice in the Jamfile?  
  
```  
if <threadapi>win32 in $(properties)  
    {  
        result += <define>BOOST_THREAD_WIN32 ;  
    }  
```

---

## Comment 14

> Username: Kojoley  
> Created at: 2018-02-28 20:29:27 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369372238  

No. How it could affect `threadapi=pthread`?

---

## Comment 15

> Username: viboes  
> Created at: 2018-02-28 21:22:04 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369387253  

No way. This is for the possible win32 issue.

---

## Comment 16

> Username: viboes  
> Created at: 2018-02-28 21:25:41 UTC  
> Updated at: 2018-03-01 08:18:03 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369388305  

Please, could you remove the bin_v2 folder again and send me complete log of  
  
```  
../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread -d2  
```  
  
I would like to be sure we don't use different -D.

---

## Comment 17

> Username: Kojoley  
> Created at: 2018-02-28 21:41:35 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369392811  

[full.log.zip](https://github.com/boostorg/thread/files/1768963/full.log.zip)

---

## Comment 18

> Username: viboes  
> Created at: 2018-03-01 07:57:19 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369507133  

It is weird. the executable is not linking with boost_thread  
```  
    "g++"  -Wl,-rpath   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link   
    -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi"   
    -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.exe"   
    -Wl,--start-group "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\test_thread.o"   
    "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\build-no\threadapi-pthread\threading-multi\winrt_init.o"  
    "..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a"   
    "..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a"   
    -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -g -mthreads -m64   
```  
Here is is mine on MacOs and clang  
  
  
```  
  
    "/Users/viboes/clang/clang+llvm-5.0.0-x86_64-apple-darwin/bin/clang++"     
    -o "../../../bin.v2/libs/thread/test/test_thread.test/clang-darwin-5.0.0_1z/debug/threadapi-pthread/threading-multi/test_thread"   
    "../../../bin.v2/libs/thread/test/test_thread.test/clang-darwin-5.0.0_1z/debug/threadapi-pthread/threading-multi/test_thread.o"   
    "../../../bin.v2/libs/thread/test/test_thread.test/clang-darwin-5.0.0_1z/debug/threadapi-pthread/threading-multi/winrt_init.o"   
    "../../../bin.v2/libs/thread/build/clang-darwin-5.0.0_1z/debug/threadapi-pthread/threading-multi/libboost_thread.dylib"   
    "../../../bin.v2/libs/chrono/build/clang-darwin-5.0.0_1z/debug/threading-multi/libboost_chrono.dylib"   
    "../../../bin.v2/libs/test/build/clang-darwin-5.0.0_1z/debug/threading-multi/libboost_unit_test_framework.dylib"   
    "../../../bin.v2/libs/system/build/clang-darwin-5.0.0_1z/debug/threading-multi/libboost_system.dylib"          
    -fPIC -m64 -g -std=c++1z -stdlib=libc++ -L/Users/viboes/clang/clang+llvm-5.0.0-x86_64-apple-darwin/lib  
```

---

## Comment 19

> Username: Kojoley  
> Created at: 2018-03-01 12:35:02 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369578139  

> It is weird. the executable is not linking with boost_thread  
  
Maybe because of that?  
  
```  
        if <target-os>windows in $(properties)  
        {  
            local paths = [ win32_pthread_paths $(properties) ] ;  
            if $(paths)  
            {  
                result += $(paths) ;  
            }  
            else  
            {  
                result = <build>no ;  
            }  
        }  
```  
  
After adding this to `user-config.jam`:  
  
```  
PTW32_INCLUDE = "G:\\msys64\\usr\\include" ;  
PTW32_LIB = "G:\\msys64\\usr\\lib" ;  
```  
  
and changing that  
  
```  
        if <toolset>gcc in $(properties)  
        {  
            libname = lib$(libname)GC2.a ;  
        }  
```  
  
to this  
  
```  
        if <toolset>gcc in $(properties)  
        {  
            libname = lib$(libname)GC2.a lib$(libname).a ;  
        }  
```  
  
I have got the following  
  
```  
$ ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 4372 targets...  
...updating 11 targets...  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\future.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\pthread\once.o  
gcc.compile.c++ ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\pthread\thread.o  
gcc.link.dll ..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_thread_pthread-gcc64-mt-d-x64-1_67.dll.a  
gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_thread.exe  
/usr/lib/../lib/libmsys-2.0.a(libcmain.o): In function `main':  
/msys_scripts/msys2-runtime/src/msys2-runtime/winsup/cygwin/lib/libcmain.c:37: undefined reference to `WinMain'  
/msys_scripts/msys2-runtime/src/msys2-runtime/winsup/cygwin/lib/libcmain.c:37:(.text.startup+0x94): relocation truncated to fit: R_X86_64_PC32 against undefined symbol `WinMain'  
collect2: error: ld returned 1 exit status  
  
    "g++"  -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/thread/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/chrono/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/system/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/test/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/thread/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -Wl,-rpath-link -Wl,"/C:/Working/Repositories/boost/bin.v2/libs/timer/build/gcc-gnu-6.4.0/debug/threadapi-pthread/threading-multi" -o "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_thread.exe" -Wl,--start-group "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_thread.o" "..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\winrt_init.o" "..\..\..\bin.v2\libs\thread\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_thread_pthread-gcc64-mt-d-x64-1_67.dll.a" "..\..\..\bin.v2\libs\chrono\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_chrono-gcc64-mt-d-x64-1_67.dll.a" "..\..\..\bin.v2\libs\test\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_unit_test_framework-gcc64-mt-d-x64-1_67.dll.a" "..\..\..\bin.v2\libs\system\build\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\libboost_system-gcc64-mt-d-x64-1_67.dll.a" "G:\msys64\usr\lib\libpthread.a"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -g -mthreads -m64  
  
...failed gcc.link ..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi\test_thread.exe...  
...skipped <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi>test_thread.run for lack of <p..\..\..\bin.v2\libs\thread\test\test_thread.test\gcc-gnu-6.4.0\debug\threadapi-pthread\threading-multi>test_thread.exe...  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 8 targets...  
```

---

## Comment 20

> Username: viboes  
> Created at: 2018-03-01 17:05:10 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369660445  

I suspect is because we are linking `winrt_init.o`, but I'm not sure .  
  
```  
rule thread-run ( sources )  
{  
    sources = $(sources) winrt_init.cpp ;  
```  
  
It seems that the original authors Boost.Config and Boost.Predef considered cygwin a WINDOWS platform. That seems to be in contradiction to use `threadapi=pthread` :(  
  
In `winrt_init.cpp` we have  
  
```  
#include <boost/predef/platform.h>  
  
#if BOOST_PLAT_WINDOWS_RUNTIME  
//...  
  
```  
  
We need some help from Boost.Config or Boost.Predef to know exactly which platform we are running on in this case.

---

## Comment 21

> Username: viboes  
> Created at: 2018-03-01 17:06:38 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369660951  

I don't remember where you are with `threadapi=win32` or letting the build system deduce the `threadapi` property.

---

## Comment 22

> Username: Kojoley  
> Created at: 2018-03-01 18:09:46 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369679648  

> I suspect is because we are linking `winrt_init.o`, but I'm not sure .  
  
Indeed. After removing `winrt_init.cpp` it compiles and test passes.  
  
> I don't remember where you are with `threadapi=win32`  
  
The last post was with `threadapi=pthread` as you can see from the command line (`$ ../../../b2.exe -j2 toolset=gcc threadapi=pthread test_thread`)  
  
Boost.Build does not bother with threading library on cygwin/msys and always links `-mthreads` https://github.com/boostorg/build/blob/0d5e76ebf27bd2b0b7f3aa1a5c573dd37ab8ea5a/src/tools/gcc.jam#L378, should not it be:  
```  
threading-flags <target-os>cygwin,<threadapi>win32 : -mthreads ;  
threading-flags <target-os>cygwin,<threadapi>pthread  : -pthreads ;  
```  
  
and Boost.Thread `win32_pthread_paths` for `<target-os>cygwin` should not return `<build>no` if there is no `PTW32_INCLUDE`/`PTW32_LIB`?

---

## Comment 23

> Username: Kojoley  
> Created at: 2018-03-01 18:13:14 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369680674  

I am actually not interested in MSYS/Cygwin, if it is not important platform for you we can both stop wasting the time on this.

---

## Comment 24

> Username: viboes  
> Created at: 2018-03-02 09:24:29 UTC  
> Url: https://github.com/boostorg/thread/issues/197#issuecomment-369867320  

It is not important for me now.   
If you are not interested neither, we will wait until someone else has the patience to help to setup this correctly.  
Please, be free to close the issue if you don't want it to be fixed.
