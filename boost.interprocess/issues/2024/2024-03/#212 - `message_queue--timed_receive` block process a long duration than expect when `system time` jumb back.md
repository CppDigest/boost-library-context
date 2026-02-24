# #212 - `message_queue::timed_receive` block process a long duration than expect when `system time` jumb back [Closed]

> Username: jerryhuangcc  
> Created at: 2024-03-04 09:09:24 UTC  
> Updated at: 2024-08-05 21:48:30 UTC  
> Closed at: 2024-08-05 21:48:30 UTC  
> Url: https://github.com/boostorg/interprocess/issues/212  

In my project, I use `message_queue::timed_receive` to handle inter process messaging.   
  
But in issue scenario, my system clock falled back to 2mins, then `timed_receive` waited for more than 1s already set in code. This behaviour is not expected.  
  
```c++  
while (true)  
{  
     // var defination  
     if (message_queue.timed_receive(&message, sizeof(message), received_size, priority, std::chrono::seconds(1)))   
    {  
           handle(message);   
    }  
  
    // other logics  
     
}  
```  
  
My question is for `message_queue::timed_receive`, does it support steady_clock which is not affected by `system time` update?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-05 21:48:30 UTC  
> Url: https://github.com/boostorg/interprocess/issues/212#issuecomment-2269978162  

timed_receive expects an absolute time and you are passing a duration. In any case, even if an absolute time is passed that depends on which time the underlying operating system uses. In POSIX systems, the default clock is the real-time clock (CLOCK_REALTIME):  
  
https://pubs.opengroup.org/onlinepubs/009695399/functions/pthread_cond_timedwait.html  
  
In some recent systems (Linux) it might be possible to configure POSIX condition variables to use CLOCK_MONOTONIC instead but in any case, we can't have a general guarantee  about steady clock (monotonic) support.
