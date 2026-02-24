# #200 - Condition variable and Mutex blocks after one process crashes and restart [Closed]

> Username: lioriz  
> Created at: 2023-08-24 08:50:39 UTC  
> Updated at: 2026-01-07 09:17:25 UTC  
> Closed at: 2026-01-07 09:17:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/200  

See: [Stack overflow question](https://stackoverflow.com/questions/76956994/boost-ipc-condition-variable-blocks-after-one-process-crashes-and-restart)  
  
I have an `interprocess` `condition variable` to sync my processes, there is one `notifier` that `notify` and two `waiters` that `timed_wait` for `notification` or `timeout`.  
  
If one of the `waiters` crashes and restarts, the three processes are blocked.  
  
The way to fix it is to kill them, delete the shared memory, and restart them.  
  
Processes sync shared memory class:  
  
```  
class ProcessSync {  
public:  
    static ProcessSync *OpenOrCreate(bip::managed_shared_memory &sync_segment, const std::string& sync_name) {  
        while (true) {  
            std::string object_name = sync_name + OBJECT_NAME;  
            std::string lock_name = sync_name + LOCK;  
            try {  
                bip::named_mutex sync_lock{bip::open_or_create, lock_name.c_str()};  
                bip::scoped_lock<bip::named_mutex> lock(sync_lock, boost::get_system_time() + boost::posix_time::seconds(1));  
                if (lock) {  
                    sync_segment = {bip::open_or_create, sync_name.c_str(),  
                                               int(sizeof(ProcessSync) * 10)};  
                    return (sync_segment.find_or_construct<ProcessSync>(  
                        (object_name.c_str()))());  
                } else {  
                    LOG(WARNING) << "can't acquire lock for opening the " + sync_name;  
                    throw std::exception();  
                }  
            } catch (std::exception &e) {  
                LOG(WARNING) << "ProcessSync could not be created.";  
                LOG(WARNING) << e.what();  
                RemoveShmByName(sync_name);  
                RemoveLockByName(lock_name);  
                sleep(10);  
            }  
        }  
    }  
  
    static ProcessSync *Open(bip::managed_shared_memory &sync_segment, const std::string& sync_name) {  
        while (true) {  
            std::string object_name = sync_name + OBJECT_NAME;  
            std::string lock_name = sync_name + LOCK;  
            try {  
                bip::named_mutex sync_lock{bip::open_only, lock_name.c_str()};  
                bip::scoped_lock<bip::named_mutex> lock(sync_lock, bip::try_to_lock);  
                if (lock) {  
                    sync_segment = bip::managed_shared_memory(bip::open_only, sync_name.c_str());  
                    return sync_segment.find<ProcessSync>(object_name.c_str()).first;  
                } else {  
                    LOG(WARNING) << "can't acquire lock for opening the shm " + sync_name;  
                    throw std::exception();  
                }  
            } catch (std::exception &e) {  
                LOG(WARNING) << sync_name + " shm not set yet.";  
                LOG(WARNING) << e.what();  
                sleep(5);  
            }  
        }  
    }  
  
    static void RemoveLockByName(const std::string & lock_name) {  
        try {  
            bip::named_mutex::remove(lock_name.c_str());  
            LOG(INFO) << lock_name + " deleted.";  
        } catch (std::exception &e) {  
            LOG(WARNING) << lock_name + " NOT deleted.";  
            LOG(WARNING) << e.what();  
        }  
    }  
      
    static void RemoveShmByName(const std::string & sync_name) {  
        try {  
            bip::shared_memory_object::remove(sync_name.c_str());  
            LOG(INFO) << sync_name + " deleted.";  
        } catch (std::exception &e) {  
            LOG(WARNING) << sync_name + " NOT deleted.";  
            LOG(WARNING) << e.what();  
        }  
      
    }  
  
    void WaitForSyncNotification(int timeout = CV_WAIT_TIMEOUT) {  
        bip::scoped_lock<boost::interprocess::interprocess_mutex> lock(process_sync_cv_mutex_);  
        bpt::ptime wait_timeout = bpt::second_clock::local_time() + bpt::seconds(timeout);  
        process_sync_cv_.timed_wait(lock, wait_timeout);  
    }  
  
    void NotifySync(){  
        process_sync_cv_.notify_all();  
    }  
  
private:  
    bip::interprocess_mutex process_sync_cv_mutex_;  
    bip::interprocess_condition process_sync_cv_;  
};  
```  
  
First process `notifier`:  
  
```  
bip::managed_shared_memory sync_segment;  
ProcessSync *sync = ProcessSync::OpenOrCreate(sync_segment, "sync");  
while(){  
    //do something  
    sync->NotifySync();  
}  
```  
  
Second/Third processes `waiters`:  
  
```  
bip::managed_shared_memory sync_segment;  
ProcessSync *sync = ProcessSync::Open(sync_segment, "sync");  
while(){  
    sync->WaitForSyncNotification();  
    //do something  
}  
```  
  
Boost version `1.78.0`  
  
Linux `Ubuntu 18.04.5 LTS`  
  
Arch `aarch64`  
  
On a normal run, everything works as expected, and when one `waiter` crashes (non-boost related issues) and restarts the three processes are blocked on the `condition variable`, `notifier` blocked on `notify`, first `waiter` blocked on `timed_wait`, crashed `waiter` blocked on `mutex lock`.  
  
In case the `notifier` is up but not in the while loop yet, the `waiters` can crash and restart, and the behavior of waiting until timeout will continue, but when the `notifier` enters the while loop and `notify` the three of them get stuck, `notifier` block on `notify`, first `waiter` block on `timed_wait`, crashed waiter block on `mutex lock`.  
  
It happened just when I crashed one of the `waiters` when it blocked on `timed_wait`, not when I crashed the `notifier`.

---

## Comment 1

> Username: wangbinio  
> Created at: 2023-12-10 15:04:32 UTC  
> Url: https://github.com/boostorg/interprocess/issues/200#issuecomment-1848990244  

i have the same question with the simple code.  
```cpp  
  
#include <boost/interprocess/sync/named_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <boost/interprocess/sync/named_condition.hpp>  
#include <thread>  
#include <exception>  
#include <iostream>  
  
using namespace std;  
using namespace boost::interprocess;  
  
int main(int argc, char *argv[])  
{  
    try {  
    if (argc == 1) {  
        named_condition::remove("my_named_condition");  
        named_mutex::remove("my_named_mutex");  
        named_mutex mutex(create_only, "my_named_mutex");  
        named_condition condition(create_only, "my_named_condition");  
  
        while (true) {  
            cout << "work..." << endl;  
            this_thread::sleep_for(chrono::seconds(1));  
            condition.notify_all();  
        }  
    } else {  
        named_mutex mutex(open_only, "my_named_mutex");  
        named_condition condition(open_only, "my_named_condition");  
        while (true) {  
            cout << "wait..." << endl;  
            scoped_lock<named_mutex> lock(mutex);  
            condition.wait(lock);  
            cout << "notified" << endl;  
        }  
    }  
    } catch (std::exception &e) {  
        std::cout << e.what() << std::endl;  
    }  
    return 0;  
}  
```  
  
one notifier and some waiter, when terminate a waiter, then the notifier and others all blocking.  
i want use named_condition to sync process, but meet this question, and i don't konw how to solve it.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2026-01-07 09:17:25 UTC  
> Url: https://github.com/boostorg/interprocess/issues/200#issuecomment-3717966054  

Note that there is no robust condition variable implementation in any OS so there is no way Boost.Interprocess can handle it. One alternative would be to use timed waits to see if the OS implementation detects an error when trying to relock the mutex that was left inconsistent, but note that the mutex would not be locked (EOWNERDEAD does NOT lock the mutex) when returning from the wait, so any operation would be undefined, as loops around the wait are designed assuming the mutex is locked. So a real mess.
