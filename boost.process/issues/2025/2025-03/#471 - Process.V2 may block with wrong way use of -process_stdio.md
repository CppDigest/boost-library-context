# #471 - Process.V2 may block with wrong way use of "process_stdio" [Open]

> Username: atomictoquark  
> Created at: 2025-03-28 08:23:44 UTC  
> Updated at: 2025-10-07 10:58:17 UTC  
> Url: https://github.com/boostorg/process/issues/471  

```  
#define WIN32_LEAN_AND_MEAN  
#define BOOST_PROCESS_VERSION 2  
#include <boost/process.hpp>  
#include <iostream>  
  
#if defined(BOOST_PROCESS_V2_WINDOWS)  
#pragma comment(lib,"ntdll.lib")  
  
//for MSVC 143  
#if defined(_MT) //MultiThread Static  
#   if defined(_DEBUG)  
#       pragma comment(lib,"libboost_process-vc143-mt-sgd-x64-1_87.lib")  
#   endif  
#endif  
#endif  
  
using namespace boost;  
namespace bp = boost::process::v2;  
int main()  
{  
    asio::io_context ctx;  
    std::string output;  
    output.resize(1000);  
    system::error_code ec;  
  
    {  
        //bad way  
        asio::readable_pipe rp2{ ctx };  
  
        //this io must declare inside proc like bp::process proc(ctx,xx,xxx,bp::process_stdio{{...},{...},{..}}}  
        bp::process_stdio io = bp::process_stdio{ nullptr, rp2, nullptr };  
        //due to process_io_binding@stdio.hpp hold this pip handle with a unique_ptr,  
        //this make the life time as long as the var 'proc' bellow  
        //and cannot clean the handle passed to the child,after CreateProcessW  
        //thus read_some can't get ERROR_PIPE_BROKEN(because there still have a valid PIPE handle in 'io')  
  
        //use SysinternalsSuite/procexp64 FIND->FIND Handle or DLL to search 'asio'   
        //after child process exit.it should not valid.  
        bp::process proc(ctx, R"(C:\Program Files\Git\bin\git.exe)", { "-v" }, io);  
        proc.detach();  
        while (true)  
        {  
            //bad:never revice ERROR_PIPE_BROKEN  
            auto size = rp2.read_some(asio::buffer(output), ec);  
            if (ec)  break;  
            std::cout<<output.substr(0, size);  
        }  
        std::cout<<"badend\n\n";  
    }  
  
    {  
        //good way  
        asio::readable_pipe rp{ ctx };  
        bp::process proc(ctx, R"(C:\Program Files\Git\bin\git.exe)", { "-v" }, bp::process_stdio{ nullptr, rp, nullptr });  
        proc.detach();  
        while (true)  
        {  
            auto size = rp.read_some(asio::buffer(output), ec);  
            if (ec)  break;  
            std::cout<<output.substr(0, size);  
        }  
        std::cout<<"goodend\n\n";  
    }  
  
    return 0;  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-03-28 11:54:55 UTC  
> Url: https://github.com/boostorg/process/issues/471#issuecomment-2761151023  

What's the issue?

---

## Comment 2

> Username: atomictoquark  
> Created at: 2025-03-29 04:34:40 UTC  
> Updated at: 2025-03-29 04:46:21 UTC  
> Url: https://github.com/boostorg/process/issues/471#issuecomment-2763089650  

//bad:never revice ERROR_PIPE_BROKEN  
            auto size = rp2.read_some(asio::buffer(output), ec);  
situation:will not return when child process exited.bolcked here.  
except: return with ec(ERROR_PIPE_BROKEN) when child process exited.  
reason:io hold the pipe handle passed to child(created by CreateFileW),it should be closed after CreateProcessW imediately,but now its in io's unique_ptr.  
diffrent between bad-good:good way correctly called ~process_io()  
bad way move contructor not called,so native handle lifetime is too long  to make the problem.

---

## Comment 3

> Username: aaeberharter  
> Created at: 2025-10-07 10:58:17 UTC  
> Url: https://github.com/boostorg/process/issues/471#issuecomment-3376372534  

This works for me:  
```C++  
boost::process::process_stdio stdio;  
/*  
Initialize stdio pipe handles ...  
*/  
auto proc = boost::process::process(ctx, path, args, stdio);  
stdio = {};  
/*  
Read from the pipes ...  
*/  
```
