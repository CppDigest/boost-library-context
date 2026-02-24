# #368 - Linux and windows behave differently [Closed]

> Username: zhixingchen0629  
> Created at: 2024-05-10 12:45:48 UTC  
> Updated at: 2025-06-02 09:54:39 UTC  
> Closed at: 2025-06-02 09:54:39 UTC  
> Url: https://github.com/boostorg/process/issues/368  

Hello, thanks a lot for the great library. Recently I have been plagued by calls to the interface on the windows side. I want to create a child process with `as_user_ launch`, and then the standard input and standard output of the child process can be captured by the parent process. Here is my demo. In this demo, the windows side will get stuck in the read pipe, but not on the linux side. What is the reason for this? Hello_world.exe in windows outputs the user name and 'hello world', of the current process, while the script on the linux side outputs' hello world',. Part of the implementation is to refer to the test case call in the process library. I did not find out how to pass parameters on the windows side to redirect the standard output and standard error of the child process.  
  
# which version  
`boost 1.85.0`  
# what i desire  
i want to get the child process stdout and stderr msg through pipe in both linux and windows  
# what i got  
on windows side, the program was Infinite waiting  
# code  
- linux  
```cpp  
#include <boost/process/v2.hpp>  
  
#include <boost/asio/read.hpp>  
#include <boost/asio/readable_pipe.hpp>  
#include <boost/asio/streambuf.hpp>  
#include <boost/system/error_code.hpp>  
  
#include <iostream>  
#include <string>  
#include <vector>  
  
namespace bpv = boost::process::v2;  
namespace asio = boost::asio;  
  
int main()  
{  
    asio::io_context ctx;  
    asio::readable_pipe rp(ctx);  
    asio::writable_pipe wp(ctx);  
    asio::connect_pipe(rp, wp);  
  
    try  
    {  
        auto launcher = bpv::default_process_launcher();  
        boost::system::error_code ec;  
  
        auto p = launcher(ctx, ec, "/bin/bash", std::vector<std::string>{"/tmp.sh"}, bpv::process_stdio{nullptr, wp, wp});  
        if (ec)  
        {  
            std::cout << "failed to launcher process" << std::endl;  
            return -1;  
        }  
  
        wp.close();  
        asio::streambuf st;  
        std::istream is{&st};  
  
        auto sz = asio::read(rp, st, ec);  
        while (ec == asio::error::interrupted)  
            sz += asio::read(rp, st, ec);  
  
        std::string output(std::istreambuf_iterator<char>(is), {});  
        std::cout << "screen: " << output << std::endl;  
    }  
    catch (const std::exception& e)  
    {  
        std::cout << "exception occur" << std::endl;  
        std::cerr << e.what() << '\n';  
    }  
    catch (...)  
    {  
        std::cout << "exception was throwed" << std::endl;  
    }  
  
    return 0;  
}  
```  
- windows  
```cpp  
#include <iostream>  
#include <string>  
#include <vector>  
  
// boost asio.  
#include <boost/asio.hpp>  
#include <windows.h>  
  
// boost process v2.  
#include <boost/process/v2.hpp>  
#include <boost/process/v2/windows/as_user_launcher.hpp>  
  
// gse.  
#include "tools/time.h"  
  
namespace bpv = boost::process::v2;  
namespace asio = boost::asio;  
  
int main(int argc, char** argv)  
{  
  
    HANDLE token = INVALID_HANDLE_VALUE;  
  
    LPCWSTR pszUsername = L"user";  
    LPCWSTR pszPassword = L"passwd";  
  
    if (LogonUserW(pszUsername, nullptr, pszPassword, LOGON32_LOGON_INTERACTIVE, LOGON32_PROVIDER_DEFAULT, &token) == 0)  
    {  
        std::cout << "failed to call LogonUser, err-msg: " << GetLastError() << std::endl;  
        return -1;  
    }  
  
    std::cout << "success to call LogonUser, token: " << token << std::endl;  
  
    auto launcher = bpv::windows::as_user_launcher(token);  
    launcher.creation_flags = CREATE_SUSPENDED | EXTENDED_STARTUPINFO_PRESENT;  
  
    asio::io_context ctx;  
    asio::readable_pipe rp{ctx};  
    asio::writable_pipe wp{ctx};  
    asio::connect_pipe(rp, wp);  
  
    std::string path = "D:\\hello_world.exe";  
  
    boost::system::error_code ec;  
    auto proc = launcher(ctx, ec, path, std::vector<std::string>{}, bpv::process_stdio{nullptr, wp, wp});  
    if (ec)  
    {  
        std::cout << "failed to launcher process" << std::endl;  
        return -1;  
    }  
  
    std::cout << "success to launcher process" << std::endl;  
  
    wp.close();  
    asio::streambuf st;  
    std::istream is{&st};  
  
    auto sz = asio::read(rp, st, ec);  
    while (ec == asio::error::interrupted)  
        sz += asio::read(rp, st, ec);  
  
    std::string output(std::istreambuf_iterator<char>(is), {});  
    std::cout << "screen: " << output << std::endl;  
  
    std::cout << "success to call as_user_launcher" << std::endl;  
  
    return 0;  
}  
```  
#

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-05-10 13:28:26 UTC  
> Url: https://github.com/boostorg/process/issues/368#issuecomment-2104612098  

Is it possible that's because of the `CREATE_SUSPENDED` flag? Shouldn't you call resume?

---

## Comment 2

> Username: zhixingchen0629  
> Created at: 2024-05-11 02:51:09 UTC  
> Url: https://github.com/boostorg/process/issues/368#issuecomment-2105498221  

hi, when i call the resume API, i found a bug in free error code version，show as follow picture,  
 https://github.com/boostorg/process/blob/develop/include/boost/process/v2/detail/process_handle_windows.hpp#L212  
<img width="781" alt="image" src="https://github.com/boostorg/process/assets/41314159/ad26ba3d-ff6f-4b3c-92b4-4b6cf403db6c">  
here is my pr link:  
https://github.com/boostorg/process/pull/369

---

## Comment 3

> Username: nigels-com  
> Created at: 2025-01-18 22:33:31 UTC  
> Url: https://github.com/boostorg/process/issues/368#issuecomment-2600217656  

This issue resolved via #369 ?

---

## Comment 4

> Username: zhixingchen0629  
> Created at: 2025-05-27 02:55:32 UTC  
> Url: https://github.com/boostorg/process/issues/368#issuecomment-2910959451  

> This issue resolved via [#369](https://github.com/boostorg/process/pull/369) ?  
  
yes
