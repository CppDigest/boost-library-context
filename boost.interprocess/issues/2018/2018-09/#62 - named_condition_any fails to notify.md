# #62 - named_condition_any fails to notify [Closed]

> Username: shayosler  
> Created at: 2018-09-12 14:48:45 UTC  
> Updated at: 2018-10-04 20:59:33 UTC  
> Closed at: 2018-10-04 20:59:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/62  

The `shm_named_condition_any` class' `wait`, `timed_wait`, `notify_one`, and `notify_all` functions are implemented using the local instance of `internal_condition_members`, `m_cond`, instead of using the one in shared memory, this causes `named_condition_any` to never send/receive notifications

---

## Comment 1

> Username: shayosler  
> Created at: 2018-09-12 16:18:35 UTC  
> Updated at: 2018-09-12 16:45:06 UTC  
> Url: https://github.com/boostorg/interprocess/issues/62#issuecomment-420709406  

Code to replicate issue. Run one instance with no arguments to receive notifications, run one instance with 1 or more arguments to send notifications. No notifications are ever received by receiver  
```  
#include <iostream>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/sync/named_condition_any.hpp>  
#include <boost/interprocess/sync/file_lock.hpp>  
#include <boost/thread.hpp>  
int main(int argc, char** argv)  
{  
  boost::interprocess::file_lock flock("/tmp/flock");  
  
  // Create condition variable  
  boost::interprocess::named_condition_any cond(boost::interprocess::open_or_create,  
                                                "cond_any");  
  
  while(true)  
  {  
    if(argc > 1)  
    {// Sender  
      std::cout << "Notifying" << std::endl;  
      cond.notify_all();  
      boost::this_thread::sleep_for(boost::chrono::seconds(1));  
    }  
    else  
    {// Receiver  
      std::cout << "Acquiring lock..." << std::endl;  
      boost::interprocess::scoped_lock<boost::interprocess::file_lock> lock(flock);  
      std::cout << "Locked. Waiting for notification..." << std::endl;  
      cond.wait(lock);  
      std::cout << "Notified!" << std::endl;  
    }  
  }  
  
  return 0;  
}  
```

---

## Comment 2

> Username: shayosler  
> Created at: 2018-09-12 16:22:20 UTC  
> Updated at: 2018-09-12 17:35:33 UTC  
> Url: https://github.com/boostorg/interprocess/issues/62#issuecomment-420710634  

It appears that the tests for `named_condition_any` does not catch this because the tests are implemented using `condition_test_template.hpp`, which tests condition variables by spinning off two threads that share a pointer to the same condition variable object. This doesn't test the interaction of two separate named condition variables
