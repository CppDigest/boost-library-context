# #374 - [process V2]: Centos and Macos behave differently [Closed]

> Username: zhixingchen0629  
> Created at: 2024-05-28 08:00:30 UTC  
> Updated at: 2024-12-05 12:34:52 UTC  
> Closed at: 2024-12-05 12:34:52 UTC  
> Url: https://github.com/boostorg/process/issues/374  

Hi, thanks a lot for the great library. Recently I encountered a problem, the same code snippet, I got different results in centos and linux.  
In mac, the sleep function of the parent process is interrupted after the child process exits, while in linux, the exit of the child process does not break the sleep function of the parent process.  
  
# which version  
`boost version 1.85.0`  
  
# what i desire  
Do not interrupt the sleep function of the parent process when the child process exits  
  
# code  
```cpp  
#include <boost/process/v2.hpp>  
  
// boost v2.  
#include <boost/asio/read.hpp>  
#include <boost/asio/readable_pipe.hpp>  
#include <boost/asio/streambuf.hpp>  
#include <boost/system/error_code.hpp>  
#include <boost/filesystem.hpp>  
  
#include <iostream>  
#include <string>  
#include <vector>  
  
#include <unistd.h>  
  
namespace bpv = boost::process::v2;  
namespace asio = boost::asio;  
  
int work()  
{  
    asio::io_context ctx;  
    asio::readable_pipe rp(ctx);  
    asio::writable_pipe wp(ctx);  
    asio::connect_pipe(rp, wp);  
  
    boost::process::v2::process tmp(ctx);  
    try  
    {  
        auto launcher = bpv::default_process_launcher();  
        boost::system::error_code ec;  
  
        auto p = launcher(ctx, ec, "/bin/bash", std::vector<std::string>{"-l", "-c", "/scripts/normal_exit.sh"}, bpv::process_stdio{nullptr, wp, wp});  
        if (ec)  
        {  
            std::cout << "failed to launcher process" << std::endl;  
            return -1;  
        }  
        std::cout << "finish launcher child process" << std::endl;    
  
        // std::atomic<bool> isStart = true;  
        wp.close();  
  
        std::thread t([&rp](){  
            std::cout << "step in another thread" << std::endl;  
  
            asio::streambuf st;  
            std::istream is{&st};  
            boost::system::error_code ec;  
  
            auto sz = asio::read(rp, st, ec);  
            while (ec == asio::error::interrupted)  
                sz += asio::read(rp, st, ec);  
  
            std::string output(std::istreambuf_iterator<char>(is), {});  
            std::cout << "screen: " << output << std::endl;  
  
            std::cout << "step out thread" << std::endl;  
        });   
          
  
        std::cout << "before sleep 10 seconds xxx" << std::endl;  
  
        // ::sleep(10);  
  
        std::cout << "after sleep 10 seconds xxx" << std::endl;  
  
  
        if(t.joinable()){  
            t.join();  
        }  
  
  
        std::cout << "before sleep 10 seconds" << std::endl;  
  
        // ::sleep(10);  
  
        std::cout << "after sleep 10 seconds" << std::endl;  
  
        rp.close();  
  
    }  
    catch (const std::exception &e)  
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
  
  
int main(){  
  
    std::thread t(work);  
  
    std::cout << "start sleep 10" << std::endl;  
  
    std::cout << "sleep time: " << ::sleep(100) << std::endl;  
  
    std::cout << "finish sleep 10" << std::endl;  
  
    if(t.joinable()){  
        t.join();  
    }  
  
    return 0;  
}  
```  
the content of normal_exit.sh:  
```shell  
#! /bin/bash  
  
echo "normal exit"  
  
echo "current user: $(whoami)"  
  
echo "bk gse test"  
  
echo "finish"  
```  
## output in macos  
```  
start sleep 10  
sleep time: finish launcher child process  
before sleep 10 seconds xxx  
after sleep 10 seconds xxx  
step in another thread  
screen: normal exit  
current user: bkdevops  
bk gse test  
finish  
  
step out thread  
before sleep 10 seconds  
after sleep 10 seconds  
99  
finish sleep 10  
```  
  
## output in centos  
```  
start sleep 10  
sleep time: this is on_setup  
this is on_success  
finish launcher child process  
before sleep 10 seconds xxx  
after sleep 10 seconds xxx  
step in another thread  
screen: normal exit  
current user: zhixingchen  
bk gse test  
finish  
  
step out thread  
before sleep 10 seconds  
after sleep 10 seconds  
0  
finish sleep 10  
```  
  
Is there no exit signal to block the child process on the macos side?

---

## Comment 1

> Username: zhixingchen0629  
> Created at: 2024-05-28 08:37:35 UTC  
> Url: https://github.com/boostorg/process/issues/374#issuecomment-2134652149  

I am confused about the signals of some functions in process_handle_signal.hpp. As shown in the figure：  
<img width="320" alt="image" src="https://github.com/boostorg/process/assets/41314159/52a4448d-1027-4d19-9e96-240325b494f7">  
  
<img width="317" alt="image" src="https://github.com/boostorg/process/assets/41314159/55624cc0-7c8a-4cae-aad2-d2a78cc35fec">  
  
<img width="366" alt="image" src="https://github.com/boostorg/process/assets/41314159/ea2e0351-7ef7-43ce-ae51-730a21275ecf">  
  
Are these functions sending the wrong signal?  
there is my PR link:  
https://github.com/boostorg/process/pull/375

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-09-26 15:13:12 UTC  
> Url: https://github.com/boostorg/process/issues/374#issuecomment-2377257389  

is this issue completed?

---

## Comment 3

> Username: zhixingchen0629  
> Created at: 2024-12-05 08:08:01 UTC  
> Url: https://github.com/boostorg/process/issues/374#issuecomment-2519525340  

> is this issue completed?  
  
yeah, it is completed
