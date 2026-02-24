# #73 - named_condition_any does not appear to notify [Open]

> Username: jpherbst  
> Created at: 2018-12-06 22:44:05 UTC  
> Updated at: 2018-12-06 22:44:05 UTC  
> Url: https://github.com/boostorg/interprocess/issues/73  

I haven't been able to get named_condition_any objects to work on Linux with Boost 1.68.0.  Below is code to replicate.  When using a named_condition_any the child process will never receive the notification, when using just a named_condition the child process receives the notifications.  
  
```c++  
#include <boost/interprocess/sync/named_condition_any.hpp>  
#include <boost/interprocess/sync/named_condition.hpp>  
#include <boost/interprocess/sync/named_mutex.hpp>  
#include <boost/thread/thread_time.hpp>  
  
#include <unistd.h>  
  
#include <chrono>  
#include <thread>  
#include <iostream>  
  
namespace ip = boost::interprocess;  
  
int main()  
{  
    typedef ip::named_mutex mutex_type;  
    //typedef ip::named_condition condition_type;  
    typedef ip::named_condition_any condition_type;  
  
    mutex_type::remove("test_mutex");  
    condition_type::remove("test_condition");  
  
    pid_t pid = fork();  
    if(pid == 0)  
    { // parent process  
        mutex_type mutex(ip::open_or_create, "test_mutex");  
        condition_type condition(ip::open_or_create, "test_condition");  
  
        while(true)  
        {  
            std::this_thread::sleep_for(std::chrono::seconds(1));  
            std::cout << "signaling children" << std::endl;  
            condition.notify_all();  
        }  
    }  
    else if(pid > 0)  
    { // child process  
        mutex_type mutex(ip::open_or_create, "test_mutex");  
        condition_type condition(ip::open_or_create, "test_condition");  
          
        while(true)  
        {  
            boost::posix_time::ptime wait_time(boost::get_system_time() +  
                boost::posix_time::seconds(10));  
            ip::scoped_lock<ip::named_mutex> lock(mutex);  
            if(condition.timed_wait(lock, wait_time))  
                std::cout << "got signal from parent" << std::endl;  
            else  
                std::cout << "timeout" << std::endl;  
        }  
    }  
    else  
    {  
        std::cout << "failed to fork: " << pid << std::endl;  
        return 1;  
    }  
  
    return 0;  
}  
```
