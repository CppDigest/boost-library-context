# #476 - [v1][Windows] Launching a child process fails due to a pipe name clash with unrelated (plug-in) DLL running its own child process around the same time [Closed]

> Username: RK-BFX  
> Created at: 2025-04-09 23:55:52 UTC  
> Updated at: 2025-04-14 15:48:01 UTC  
> Closed at: 2025-04-14 15:48:01 UTC  
> Url: https://github.com/boostorg/process/issues/476  

## General  
  
Imagine an application that is a plug-in host that loads unrelated DLLs (e.g. from different vendors) as its plug-ins. If two or more such plug-ins use Boost.Process under the hood (e.g. header-only, with no `boost_process*.dll`) to launch child processes and e.g. read their output, and if it happens so that they do it around the same time (e.g. upon project opened), then chances are high that some of them will fail with error 231: `All pipe instances are busy` because each plug-in will generate and attempt using the same pipe name.  
  
## Reproduce:  
  
1. Download the following source files to the same empty folder:  
    <details><summary><i><b><tt>exe.cpp</tt></b></i></summary>  
    <p>  
      
    ```c++  
    #include <thread>  
      
    #include <Windows.h>  
      
    int main() {  
       using ProcType = void(*)();  
       HMODULE h1 = ::LoadLibraryA("DLL1.dll");  
       HMODULE h2 = ::LoadLibraryA("DLL2.dll");  
       auto p1 = reinterpret_cast< ProcType >( ::GetProcAddress( h1, "usePipe" ) );  
       auto p2 = reinterpret_cast< ProcType >( ::GetProcAddress( h2, "usePipe" ) );  
       std::thread t1( p1 ), t2( p2 );  
       t1.join();  
       t2.join();  
       return 0;  
    }  
    ```  
    </p>  
    </details>  
    <details><summary><i><b><tt>dll.cpp</tt></b></i></summary>  
  
    It also includes `#if`ed-out code for v2 as that suffers from the same issue ([ASIO#1619](https://github.com/chriskohlhoff/asio/issues/1619)).  
    <p>  
      
    ```c++  
    #include <future>  
    #include <iostream>  
      
    #define BOOST_PROCESS_VERSION 1  
    #include <boost/asio.hpp>  
    #include <boost/process.hpp>  
      
    #if BOOST_PROCESS_VERSION == 2  // link to required libraries  
    #define BOOST_LIB_NAME boost_process  // hack to auto-link Boost.Process v2  
    #define _DLL  // for BOOST_LIB_PREFIX to be empty  
    #define BOOST_DYN_LINK  // ditto  
    #include <boost/config/auto_link.hpp>  
      
    // For EnumWindows in boost::process::v2::detail::basic_process_handle_win<>::request_exit(...),  
    // for SendMessageW, GetWindowThreadProcessId in boost::process::v2::detail::enum_window(...).  
    #pragma comment(lib, "User32.lib")  
    // For RtlNtStatusToDosError, NtResumeProcess in boost::process::v2::detail::basic_process_handle_win<>::resume(...),  
    // for NtSuspendProcess in boost::process::v2::detail::basic_process_handle_win<>::suspend(...).  
    #pragma comment(lib, "NTDLL.lib")  
    #endif  
    // For SHGetFileInfoW in boost::process::v2::environment::detail::is_exec_type(...),  
    #pragma comment(lib, "Shell32.lib")  // for SHGetFileInfoW in boost::winapi::sh_get_file_info(...).  
      
    extern "C" __declspec(dllexport) void __cdecl usePipe() {  
       try {  
          namespace ba = boost::asio;  
          ba::io_context ioc;  
          std::string output;  
    #if BOOST_PROCESS_VERSION == 1  
          namespace bp = boost::process;  
          std::future< std::string > out;  
          bp::child cp( bp::search_path("cmd.exe"), bp::std_in.close(), bp::std_out > out, ioc );  
          ioc.run();  // blocks until the pipe closes  
          output = out.get();  
    #elif BOOST_PROCESS_VERSION == 2  
          namespace bp = boost::process::v2;  
          ba::readable_pipe out{ ioc };  
          bp::process cp( ioc, bp::environment::find_executable("cmd.exe"), {/* no args */},  
                          bp::process_stdio{ /*.in=*/ nullptr /* closed */, out, /*.err=*/ {/* default */} } );  
          boost::system::error_code ec;  
          ba::read( out, ba::dynamic_buffer( output ), ec );  
          // Getting ec == 109 (ERROR_BROKEN_PIPE) rather than ba::error::eof for some reason  
    #endif  
          cp.wait();  // necessary to get the exit code, at least in v1  
          std::cout << "CMD finished. Code: " << cp.exit_code() << ". Output:\n" << output << std::endl;  
       }  
       catch( std::system_error const & e ) {  
          std::cerr << "CMD failed to start. Code: " << e.code() << ". Message:\n" << e.what() << std::endl;  
       }  
       catch( std::exception const & e ) {  
          std::cerr << "CMD failed to start. Message:\n" << e.what() << std::endl;  
       }  
    }  
    ```  
    </p>  
    </details>  
    <details><summary><i><b><tt>rebuild.cmd</tt></b></i></summary>  
    <p>  
      
    ```cmd  
    @echo off  
      
    del *.obj *.dll *.exe *.map *.exp *.lib *.pdb *.idb *.ilk  
      
    :: Used `vcpkg install --triplet x64-windows-static` to fetch 'boost-process' and its dependencies  
      
    set "BOOST_ROOT=.\vcpkg_installed\x64-windows-static"  
      
    cl.exe /nologo "/I%BOOST_ROOT%\include" ^  
           /DBOOST_PROCESS_USE_STD_FS ^  
           /DBOOST_ASIO_DISABLE_VISIBILITY ^  
           /D_WIN32_WINNT=0x0A00 /DWIN32_LEAN_AND_MEAN ^  
           /std:c++17 /EHsc /ZI ^  
           /FeDLL dll.cpp ^  
           /LD /link /DEBUG:FULL "/LIBPATH:%BOOST_ROOT%\lib"  
      
    copy DLL.dll DLL2.dll  
    move DLL.dll DLL1.dll  
      
    cl.exe /nologo /ZI /FeEXE exe.cpp /link /DEBUG:FULL  
    ```  
    </p>  
    </details>  
  
    For convenience, they are attached as an archive, along with `vcpkg` configuration files I used to fetch Boost libs: [**_`Boost.Process-v1_Windows-pipe-name-clash_2025-04-09.zip`_**](https://github.com/user-attachments/files/19675039/Boost.Process-v1_Windows-pipe-name-clash_2025-04-09.zip).  
  
2. Adjust the `BOOST_ROOT` variable value in **_`rebuild.cmd`_** to point to the Boost directory that has its libs built **statically** (to model the real-life situation and avoid dependency on **_`boost_process*.dll`_**).  
  
3. Run **_`rebuild.cmd`_** — it should produce, among others, **_`EXE.exe`_** , **_`DLL1.dll`_** and **_`DLL2.dll`_**. **_`DLL1.dll`_** and **_`DLL2.dll`_** are identical and model two distinct plug-ins.  
  
4. Run **_`EXE.exe`_**.  
  
## Expected:  
  
Assuming the current directory is **_`F:\Experiments\Boost`_**, we should get some output like this:  
```txt  
CMD finished. Code: 0. Output:  
Microsoft Windows [Version 10.0.19045.5737]  
(c) Microsoft Corporation. All rights reserved.  
  
F:\Experiments\Boost>  
```  
doubled and intertwined due to concurrent execution of two threads. E.g.:  
```txt  
CMD finished. Code: CMD finished. Code: 0. Output:  
Microsoft Windows [Version 10.0.19045.5737]  
(c) Microsoft Corporation. All rights reserved.  
  
F:\Experiments\Boost>  
0. Output:  
Microsoft Windows [Version 10.0.19045.5737]  
(c) Microsoft Corporation. All rights reserved.  
  
F:\Experiments\Boost>  
```  
  
## Actual:  
  
Due to both plug-ins trying to use the same pipe name, one of the child processes will fail to launch, so we get e.g.:  
```txt  
CMD failed to start. Code: system:231. Message:  
create_named_pipe(\\.\pipe\boost_process_auto_pipe_24652_0) failed: All pipe instances are busy  
CMD finished. Code: 0. Output:  
Microsoft Windows [Version 10.0.19045.5737]  
(c) Microsoft Corporation. All rights reserved.  
  
F:\Experiments\Boost>  
```

---

## Comment 1

> Username: RK-BFX  
> Created at: 2025-04-09 23:56:31 UTC  
> Updated at: 2025-04-10 00:07:32 UTC  
> Url: https://github.com/boostorg/process/issues/476#issuecomment-2791218083  

The report is based on Boost 1.87.0 and the bug is present in the latest sources.  
I have a local fix (simple and low-risk) and will submit a PR soon.
