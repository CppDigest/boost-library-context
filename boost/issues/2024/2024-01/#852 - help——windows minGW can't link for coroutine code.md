# #852 - help——windows minGW can't link for coroutine code [Closed]

> Username: algoqt  
> Created at: 2024-01-18 05:27:21 UTC  
> Updated at: 2024-01-18 07:50:19 UTC  
> Closed at: 2024-01-18 07:50:19 UTC  
> Url: https://github.com/boostorg/boost/issues/852  

```  
g++ main2.cpp -o main.exe -IC:/Users/hongx/Downloads/boost_1_83_0 \  
-L"C:/Windows/System32" \  
-lws2_32 -lkernel32 \  
-LC:/Users/hongx/Downloads/boost_1_83_0/stage/lib \  
-lboost_coroutine-mgw13-mt-d-x32-1_83 -lboost_system-mgw13-mt-d-x32-1_83  
```  
  
I built the boost using minGW64-mingw32 ，it works for the header-only files,but when I using asio/spawn, it  turn out error，see the flowing , I google it and ask chat-gbt all comes to the -lkernel32, but donesnt work for me ,  anybody any suggestion?  
  
when I try  using  VS-ide and vcpkg to install boost, in VS it compile and run ok, this really me puzzled  ....  
  
my code is :   
  
```  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/asio/steady_timer.hpp>  
#include <iostream>  
#include <thread>  
namespace asio = boost::asio;  
  
struct MyJob {  
    MyJob() = default;  
    MyJob(const MyJob& my) { std::cout << "copy cons" << std::endl; };  
    static int i;  
    static const int j = 10;  
    void my_coroutine(asio::io_context& io, asio::yield_context yield) {  
        std::cout << "Coroutine Start" << std::endl;  
        while (i < j) {  
            asio::deadline_timer timer(io, boost::posix_time::seconds(2));  
            i++;  
            timer.async_wait(yield);  
            std::cout << "threadid-" << std::this_thread::get_id() << ":Coroutine Resumed after Timer,i=" << i << std::endl;  
        }  
        std::cout << "Coroutine End" << std::endl;  
    }  
};  
int MyJob::i = 0;  
int main() {  
    asio::io_context ioContext1;  
    asio::io_context ioContext2;  
    MyJob myjob{};  
    std::thread thread1([&ioContext1]() { ioContext1.run(); });  
    std::thread thread2([&ioContext2]() { ioContext2.run(); });  
    asio::spawn(ioContext1, std::bind(&MyJob::my_coroutine,std::ref(myjob), std::ref(ioContext1), std::placeholders::_1));  
    asio::spawn(ioContext2, std::bind(&MyJob::my_coroutine, std::ref(myjob), std::ref(ioContext2), std::placeholders::_1));  
    thread1.join();  
    thread2.join();  
    return 0;  
}  
  
```  
```  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/crt2.o:crtexe.c:(.text+0x155): undefined reference to `_imp__Sleep@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/crt2.o:crtexe.c:(.text+0x24e): undefined reference to `_imp__SetUnhandledExceptionFilter@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/crt2.o:crtexe.c:(.text+0x264): undefined reference to `_set_invalid_parameter_handler'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/crtbegin.o:cygming-crtbeg:(.text+0x12): undefined reference to `_imp__GetModuleHandleA@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/crtbegin.o:cygming-crtbeg:(.text+0x28): undefined reference to `_imp__LoadLibraryA@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/crtbegin.o:cygming-crtbeg:(.text+0x2e): undefined reference to `_imp__GetProcAddress@8'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/crtbegin.o:cygming-crtbeg:(.text+0xc6): undefined reference to `_imp__FreeLibrary@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32EiPcj[__ZN5boost6system6detail29system_category_message_win32EiPcj]+0x88): undefined reference to `_imp__FormatMessageA@28'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32EiPcj[__ZN5boost6system6detail29system_category_message_win32EiPcj]+0xf2): undefined reference to `_imp__FormatMessageW@28'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32EiPcj[__ZN5boost6system6detail29system_category_message_win32EiPcj]+0x140): undefined reference to `_imp__WideCharToMultiByte@32'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32EiPcj[__ZN5boost6system6detail29system_category_message_win32EiPcj]+0x153): undefined reference to `_imp__LocalFree@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail10local_freeD1Ev[__ZN5boost6system6detail10local_freeD1Ev]+0x12): undefined reference to `_imp__LocalFree@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32B5cxx11Ei[__ZN5boost6system6detail29system_category_message_win32B5cxx11Ei]+0x5a): undefined reference to `_imp__FormatMessageW@28'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32B5cxx11Ei[__ZN5boost6system6detail29system_category_message_win32B5cxx11Ei]+0xcf): undefined reference to `_imp__WideCharToMultiByte@32'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost6system6detail29system_category_message_win32B5cxx11Ei[__ZN5boost6system6detail29system_category_message_win32B5cxx11Ei]+0x17a): undefined reference to `_imp__WideCharToMultiByte@32'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail9win_mutexD1Ev[__ZN5boost4asio6detail9win_mutexD1Ev]+0x10): undefined reference to `_imp__DeleteCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail9win_mutex4lockEv[__ZN5boost4asio6detail9win_mutex4lockEv]+0x10): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail9win_mutex6unlockEv[__ZN5boost4asio6detail9win_mutex6unlockEv]+0x10): undefined reference to `_imp__LeaveCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail9win_mutex7do_initEv[__ZN5boost4asio6detail9win_mutex7do_initEv]+0x18): undefined reference to `_imp__InitializeCriticalSectionAndSpinCount@8'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail9win_mutex7do_initEv[__ZN5boost4asio6detail9win_mutex7do_initEv]+0x2b): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail17winsock_init_base7startupERNS2_4dataEhh[__ZN5boost4asio6detail17winsock_init_base7startupERNS2_4dataEhh]+0x5e): undefined reference to `_imp__WSAStartup@8'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail17winsock_init_base7cleanupERNS2_4dataE[__ZN5boost4asio6detail17winsock_init_base7cleanupERNS2_4dataE]+0x2a): undefined reference to `_imp__WSACleanup@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context11auto_handleD1Ev[__ZN5boost4asio6detail19win_iocp_io_context11auto_handleD1Ev]+0x1b): undefined reference to `_imp__CloseHandle@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_contextC1ERNS0_17execution_contextEib[__ZN5boost4asio6detail19win_iocp_io_contextC1ERNS0_17execution_contextEib]+0x10f): undefined reference to `_imp__CreateIoCompletionPort@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_contextC1ERNS0_17execution_contextEib[__ZN5boost4asio6detail19win_iocp_io_contextC1ERNS0_17execution_contextEib]+0x129): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context8shutdownEv[__ZN5boost4asio6detail19win_iocp_io_context8shutdownEv]+0x77): undefined reference to `_imp__SetWaitableTimer@24'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context8shutdownEv[__ZN5boost4asio6detail19win_iocp_io_context8shutdownEv]+0x1b5): undefined reference to `_imp__GetQueuedCompletionStatus@20'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context4stopEv[__ZN5boost4asio6detail19win_iocp_io_context4stopEv]+0x79): undefined reference to `_imp__PostQueuedCompletionStatus@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context4stopEv[__ZN5boost4asio6detail19win_iocp_io_context4stopEv]+0x8c): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context25post_deferred_completionsERNS1_8op_queueINS1_18win_iocp_operationEEE[__ZN5boost4asio6detail19win_iocp_io_context25post_deferred_completionsERNS1_8op_queueINS1_18win_iocp_operationEEE]+0x56): undefined reference to `_imp__PostQueuedCompletionStatus@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE[__ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE]+0x10c): undefined reference to `_imp__SetLastError@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE[__ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE]+0x164): undefined reference to `_imp__GetQueuedCompletionStatus@20'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE[__ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE]+0x171): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE[__ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE]+0x411): undefined reference to `_imp__PostQueuedCompletionStatus@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE[__ZN5boost4asio6detail19win_iocp_io_context6do_oneEmRNS1_20win_iocp_thread_infoERNS_6system10error_codeE]+0x424): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context16get_gqcs_timeoutEv[__ZN5boost4asio6detail19win_iocp_io_context16get_gqcs_timeoutEv]+0x5b): undefined reference to `_imp__VerSetConditionMask@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context16get_gqcs_timeoutEv[__ZN5boost4asio6detail19win_iocp_io_context16get_gqcs_timeoutEv]+0x8a): undefined reference to `_imp__VerifyVersionInfoA@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:\Users\hongx\AppData\Local\Temp\ccpWYrV8.o:main2.cpp:(.text$_ZN5boost4asio6detail19win_iocp_io_context14update_timeoutEv[__ZN5boost4asio6detail19win_iocp_io_context14update_timeoutEv]+0xa5): undefined reference to `_imp__SetWaitableTimer@24'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-merr.o):merr.c:(.text+0x81): undefined reference to `__acrt_iob_func'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x15): undefined reference to `__acrt_iob_func'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x44): undefined reference to `__acrt_iob_func'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x18b): undefined reference to `_imp__VirtualQuery@12'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x27c): undefined reference to `_imp__VirtualProtect@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x28a): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-pseudo-reloc.o):pseudo-reloc.c:(.text+0x340): undefined reference to `_imp__VirtualProtect@16'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x53): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x78): undefined reference to `_imp__LeaveCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0xa9): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x116): undefined reference to `_imp__LeaveCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x13d): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x159): undefined reference to `_imp__TlsGetValue@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x166): undefined reference to `_imp__GetLastError@0'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x19b): undefined reference to `_imp__LeaveCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x1f8): undefined reference to `_imp__InitializeCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingw32.a(lib32_libmingw32_a-tlsthrd.o):tlsthrd.c:(.text+0x262): undefined reference to `_imp__DeleteCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0x45): undefined reference to `_imp__DeleteCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0x80): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0xda): undefined reference to `_imp__InitializeCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0x11f): undefined reference to `_imp__Sleep@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0x151): undefined reference to `_imp__EnterCriticalSection@4'  
C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/bin/ld.exe: C:/minGW64/mingw32/bin/../lib/gcc/i686-w64-mingw32/13.2.0/../../../../i686-w64-mingw32/lib/../lib/libmingwex.a(lib32_libmingwex_a-misc.o):misc.c:(.text+0x181): undefined reference to `_imp__LeaveCriticalSection@4'  
```

---

## Comment 1

> Username: algoqt  
> Created at: 2024-01-18 07:50:19 UTC  
> Url: https://github.com/boostorg/boost/issues/852#issuecomment-1897965470  

problem solved
