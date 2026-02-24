# #168 - epoll_wait() returning too many file descriptors [Closed]

> Username: skysley  
> Created at: 2018-11-19 15:21:50 UTC  
> Updated at: 2020-12-30 00:57:19 UTC  
> Closed at: 2020-12-30 00:57:18 UTC  
> Url: https://github.com/boostorg/asio/issues/168  

Hi,  
I am not sure whether the root cause is in the boost::asio or in the implementation of epoll_wait(). However, it may be good to handle the case described below. I am using boost 1.67.0.  
   
I have a program which spawns a lot of sub-processes and a lot of threads. Communication is basically done via boost's signals and slots and via boost::interprocess::message_queue. While I was looking for a memory leak, I encountered a strange behavior. In epoll_reactor.ipp within epoll_reactor::run() one can find this code:  
  
```C++  
  epoll_event events[128];  
  int num_events = epoll_wait(epoll_fd_, events, 128, timeout);  
```  
  
The number of file descriptors should be limited to 128. However, I encountered ```num_events``` to be 232 (see attached screenshot). Until now, I haven't got an idea why this happens. However, it would be helpful if it would be checked (at least via an assertion) that ```nume_events``` is not larger than 128.  
  
[backtrace.txt](https://github.com/boostorg/asio/files/2595844/backtrace.txt)  
  
Screenshot:  
![epoll_wait_result](https://user-images.githubusercontent.com/44500899/48715877-538d3980-ec16-11e8-8488-78af039967bd.png)

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:57:17 UTC  
> Url: https://github.com/boostorg/asio/issues/168#issuecomment-752290643  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#653](https://github.com/chriskohlhoff/asio/issues/653).
