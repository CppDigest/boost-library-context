# #430 - In a multi-threaded fork process, child processes may be stuck by locks [Open]

> Username: zhixingchen0629  
> Created at: 2024-11-28 03:49:20 UTC  
> Updated at: 2025-05-15 04:31:56 UTC  
> Url: https://github.com/boostorg/process/issues/430  

system: Darwin Kernel Version 22.5.0  
  
boost version: 1.85.0  
  
  
When I use the process v2 version of the library to create child processes to perform tasks, when multiple threads create processes at the same time, there is a high probability that one of the processes will get stuck.   
  
This is my experimental results chart：  
Here we start 4 threads, and then each thread uses process v2 to create a child process at the same time.  
![Image](https://github.com/user-attachments/assets/2a53cba2-fb81-4406-99ae-6154f3c7afaa)  
  
In this figure, process 37673 is blocked.  
Running lldb, you can see that the call stack of process 37673 is as shown in the following figure. You can see that the process is waiting for a lock. From frame 9, it can be seen that the sub-process is stuck in the notify_fork operation, and the operation inside is stuck while obtaining the lock.  
  
![Image](https://github.com/user-attachments/assets/202e7bdc-cce6-44c7-b997-977d0aa6bf28)  
  
Here is the stuck position, I try to add it before this sentence.  
I try to manually release the lock here first, and this problem can be solved. The code is as follows：  
```cpp  
 boost::asio::detail::signal_state *state = boost::asio::detail::get_signal_state();  
 state->mutex_.unlock();  
```  
![Image](https://github.com/user-attachments/assets/f810cdc4-e200-48ca-9c5a-962c99e47021)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-13 04:22:33 UTC  
> Url: https://github.com/boostorg/process/issues/430#issuecomment-2540521736  

The need for `notify_fork` is not entirely clear; it is meant for scenarios when you want to resume the io_context.run() after forking.  
  
I think I'll add an option to disabled the NOTIFY_FORK call.

---

## Comment 2

> Username: mrmgxxxx  
> Created at: 2025-05-15 04:31:55 UTC  
> Url: https://github.com/boostorg/process/issues/430#issuecomment-2882209179  

great ~ 👍
