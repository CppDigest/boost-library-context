# #109 - Shared condition variable is invalid after the waiting process crashes [Closed]

> Username: ianosd  
> Created at: 2020-02-21 13:37:26 UTC  
> Updated at: 2026-01-07 09:33:53 UTC  
> Closed at: 2026-01-07 09:33:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/109  

I have two processes, synchronized using a condition variable in shared memory.  
If the process that calls wait crashes, the notifying process sometimes blocks in the notify call.  
This happens on windows, using visual C++.  
  
Here's a minimal example:  
```  
#include <boost/interprocess/shared_memory_object.hpp>  
#include <boost/interprocess/mapped_region.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/sync/interprocess_mutex.hpp>  
#include <boost/interprocess/sync/interprocess_condition.hpp>  
  
#include <iostream>  
  
struct shared_data  
{  
   boost::interprocess::interprocess_mutex mutex;  
   boost::interprocess::interprocess_condition condition;  
  
   bool test_bool = false;  
};  
  
  
int main(int argc, char *argv[])  
{  
    using namespace boost::interprocess;  
  
    if (argc == 1) {  
        shared_memory_object shm(create_only, "MySharedMemory", read_write);  
  
        shm.truncate(sizeof(shared_data));  
        mapped_region region(shm, read_write);  
        void* addr = region.get_address();  
        shared_data* data = new (addr) shared_data;  
  
        while (true) {  
            scoped_lock<interprocess_mutex> lock(data->mutex);  
            while (!data->test_bool) {  
                data->condition.wait(lock);  
            }  
            std::cout << "test_bool became true" << std::endl;  
            data->test_bool = false;  
        }  
    }  
    else {  
        shared_memory_object shm(open_only, "MySharedMemory", read_write);  
        mapped_region region(shm, read_write);  
        shared_data* data = static_cast<shared_data*>(region.get_address());  
        while (true) {  
            {  
                scoped_lock<interprocess_mutex> lock(data->mutex);  
                data->test_bool = true;  
            }  
            std::cout << "Entering notify" << std::endl;  
            data->condition.notify_one();  
            std::cout << "Exiting notify" << std::endl;  
        }  
    }  
}  
```  
  
As far as I understand, this is because the waiting thread is supposed to unlock the internal mutex of the condition, but it doesn't get to. (The generic emulation implemetation is being used)  
Any suggestions to work around this?  
A straightforward thing to do would be to allow for a timeout in the notify method. What do you think?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-06-02 22:53:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/109#issuecomment-637848961  

Doesn't BOOST_INTERPROCESS_ENABLE_TIMEOUT_WHEN_LOCKING help?

---

## Comment 2

> Username: wangbinio  
> Created at: 2023-12-10 15:35:46 UTC  
> Updated at: 2023-12-10 15:36:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/109#issuecomment-1848998066  

@ianosd ,hello，i meet the same question. have you solve it?  
  
https://github.com/boostorg/interprocess/issues/200#issuecomment-1848990244

---

## Comment 3

> Username: igaztanaga  
> Created at: 2026-01-07 09:33:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/109#issuecomment-3718023698  

There is no robust condition variable implementation in any OS, and AFIAK a known custom implementation that handles it correctly, so there is not much Boost.Interprocess can do with it, sorry.
