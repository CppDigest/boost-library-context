# #306 - boost/process/detail/posix/wait_for_exit.hpp -> wait_until -> handler_func crash [Open]

> Username: leashi  
> Created at: 2023-04-04 00:45:13 UTC  
> Updated at: 2023-06-13 16:25:07 UTC  
> Url: https://github.com/boostorg/process/issues/306  

start mgr process  
kill read process  
kill write process  
crash in  "boost/process/detail/posix/wait_for_exit.hpp"  -> "wait_until" -> "static void handler_func(int val)"  
  
  
environment  
Target: x86_64-linux-gnu  
Thread model: posix  
gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)   
boots 1.81.0  
  
  
  
mgr process (example)  
////////////////////////////////////////////////////////////////////////  
//  
#include <thread>  
#include <iostream>  
using namespace std;  
  
#include <boost/process.hpp>  
using namespace boost;  
  
void func1()  
{  
    error_code ec;  
    process::child cp;  
  
    cp = process::child("/opt/project/board/src/test/process/read/build/read");  
    cp.wait_for(std::chrono::seconds(100000), ec)  
}  
  
void func2()  
{  
    error_code ec;  
    process::child cp;  
  
    cp = process::child("/opt/project/board/src/test/process/write/build/write");  
    cp.wait_for(std::chrono::seconds(100000), ec)  
}  
  
int main()  
{  
    std::thread th1 = std::thread(func1);  
    std::thread th2 = std::thread(func2);  
  
    th1.join();  
    th2.join();  
  
    return 0;  
}  
  
  
  
  
write process (example)  
////////////////////////////////////////////////////////////////////////  
//  
#include <signal.h>  
#include <unistd.h>  
#include <iostream>  
using namespace std;  
  
int g_stop = 0;  
  
void onSignal(int sig)  
{  
    cout << "write onSignal, sig=" << sig << endl;  
  
    g_stop = 1;  
}  
  
int main()  
{  
    cout << "write start ..." << endl;  
  
    ::signal(SIGINT | SIGKILL | SIGTERM,  onSignal);  
  
    while (g_stop == 0)  
    {  
        ::usleep(1000);  
    }  
  
    cout << "write end" << endl;  
  
    return 0;  
}  
  
  
  
  
read process (example)  
////////////////////////////////////////////////////////////////////////  
//  
#include <signal.h>  
#include <unistd.h>  
#include <iostream>  
using namespace std;  
  
int g_stop = 0;  
  
void onSignal(int sig)  
{  
    cout << "read onSignal, sig=" << sig << endl;  
  
    g_stop = 1;  
}  
  
int main()  
{  
    cout << "read start ..." << endl;  
  
    ::signal(SIGINT | SIGKILL | SIGTERM,  onSignal);  
  
    while (g_stop == 0)  
    {  
        ::usleep(1000);  
    }  
  
    cout << "read end" << endl;  
  
    return 0;  
}

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-06-13 16:25:07 UTC  
> Url: https://github.com/boostorg/process/issues/306#issuecomment-1589643474  

Do you have a stack trace? I think this is already fixed.
