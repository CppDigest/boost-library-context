# #53 - boost.coroutine2 cannot generate dump [Open]

> Username: xiaoniu1235  
> Created at: 2024-09-03 11:57:03 UTC  
> Updated at: 2024-09-03 13:51:48 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/53  

windows, using boost.coroutine2, cannot generate dump (using mini dump) if a crash occurs in the coroutine function. As long as boost.coroutine2 is not used, the dump file can be generated. Is there something I'm missing?  
Here is the code example:  
```  
#include <boost/coroutine2/all.hpp>  
#include <Windows.h>  
#include <dbghelp.h>  
#include <iostream>  
  
#pragma comment(lib, "Dbghelp.lib")   
  
void CreateMiniDump(EXCEPTION_POINTERS* pep) {  
    HANDLE hFile = CreateFile(  
        "crashdump.dmp",          // File name  
        GENERIC_WRITE,             // Desired access  
        0,                         // Share mode  
        NULL,                      // Security attributes  
        CREATE_ALWAYS,             // Creation disposition  
        FILE_ATTRIBUTE_NORMAL,     // Flags and attributes  
        NULL                       // Template file  
    );  
  
    if (hFile != INVALID_HANDLE_VALUE) {  
        MINIDUMP_EXCEPTION_INFORMATION mdei;  
        mdei.ThreadId = GetCurrentThreadId();  
        mdei.ExceptionPointers = pep;  
        mdei.ClientPointers = FALSE;  
  
        BOOL rv = MiniDumpWriteDump(  
            GetCurrentProcess(),  
            GetCurrentProcessId(),  
            hFile,  
            MiniDumpNormal,  
            &mdei,  
            NULL,  
            NULL  
        );  
  
        if (rv == FALSE) {  
            std::cerr << "MiniDumpWriteDump failed: " << GetLastError() << std::endl;  
        }  
  
        CloseHandle(hFile);  
    }  
}  
  
// Exception filter to handle crashes  
LONG WINAPI MyUnhandledExceptionFilter(EXCEPTION_POINTERS* pExceptionPointers) {  
    CreateMiniDump(pExceptionPointers);  
    return EXCEPTION_EXECUTE_HANDLER;  
}  
#pragma optimize("", off)  
void CoroutineFunction(boost::coroutines2::coroutine<void>::push_type& yield) {  
    // Example code that may crash  
    std::cout << "CoroutineFunction Begin\n";  
    int* p = nullptr;  
    *p = 0; // This will cause a crash  
    std::cout << "CoroutineFunction End\n";  
}  
#pragma optimize("", on)  
int main() {  
    SetUnhandledExceptionFilter(MyUnhandledExceptionFilter);  
  
    //  
    //int* p = nullptr;  
    //*p = 1000;  
      
    // Use Boost.Coroutine2  
    try {  
        boost::coroutines2::coroutine<void>::pull_type source(  
            [](boost::coroutines2::coroutine<void>::push_type& sink) {  
                CoroutineFunction(sink);  
            }  
        );  
    }  
    catch (const std::exception& e) {  
        std::cout << "Exception: " << e.what() << std::endl;  
    }  
  
    std::cout << "sssssssssssssssssssssssssssss\n";  
  
    char ch;  
    std::cin >> ch;  
  
    return 0;  
}  
```
