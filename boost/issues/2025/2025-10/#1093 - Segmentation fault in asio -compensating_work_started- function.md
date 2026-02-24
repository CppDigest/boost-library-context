# #1093 - Segmentation fault in asio "compensating_work_started" function [Open]

> Username: wangguodong-f  
> Created at: 2025-10-24 07:51:28 UTC  
> Updated at: 2025-10-24 07:51:28 UTC  
> Url: https://github.com/boostorg/boost/issues/1093  

I encountered a SIGSEGV error when using the asio library. I checked the backtrace with GDB and found that the error occurred in the **compensating_work_started** function.  
  
```  
void scheduler::compensating_work_started()  
{  
    thread_info_base* this_thread = thread_call_stack::contains(this);  
    ++static_cast<thread_info*>(this_thread)->private_outstanding_work;  
}  
  
```
