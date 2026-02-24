# #229 - descriptor_data not guarded correctly [Closed]

> Username: tradingapps-service  
> Created at: 2019-04-30 06:48:44 UTC  
> Updated at: 2020-12-30 01:03:37 UTC  
> Closed at: 2020-12-30 01:03:36 UTC  
> Url: https://github.com/boostorg/asio/issues/229  

Hi - We are using boost 1.51.0 asio library on linux and seeing crash at times. Upon investigation we noticed that it is crashing when it tries to access a pointer which is already set to 0.  
  
function where it crashes - asio::detail::epoll_reactor::start_op  
It crashes while accessing if (descriptor_data->shutdown_) line. gdb shows descriptor_data is already set to 0.  
  
While debugging i noticed that epoll_reactor::deregister_descriptor might be setting the object to zero.  
  
Scenario - We have seen this issue happening randomly while closing the socket.  
  
Question - the mutex variable of descriptor_data object is used to guard access of this object. However this variable is marked as 0 in epoll_reactor::deregister_descriptor function, while the thread is holding the mutex_. In a different thread executing start_op function and waiting on this function will find null object when the mutex lock is freed.  
  
Shouldn't there be a check in asio::detail::epoll_reactor::start_op function, after the mutex lock is grabbed to see descriptor_data is still valid object and then access it's shutdown variable.?  
  
Also, isn't it a bad practice of marking the object as 0 but still holding the lock via it's member variable.?

---

## Comment 1

> Username: djarek  
> Created at: 2019-04-30 12:53:22 UTC  
> Url: https://github.com/boostorg/asio/issues/229#issuecomment-487940209  

`descriptor_data` is a pointer stored in each I/O object (e.g. socket). The documentation specifies that shared I/O objects are not thread-safe. The bug is in your code, most likely, due to a data race on a socket or other I/O object.  
Here's the reference to the documentation on thread-safety of sockets:  
https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/ip__tcp/socket.html  
```  
Thread Safety  
  
Distinct objects: Safe.  
  
Shared objects: Unsafe.   
```  
Side note:  Boost 1.51 is ancient, you should consider an upgrade.

---

## Comment 2

> Username: vvaltchev  
> Created at: 2019-08-25 23:10:03 UTC  
> Updated at: 2019-08-25 23:14:49 UTC  
> Url: https://github.com/boostorg/asio/issues/229#issuecomment-524672124  

I filed this feature request: https://svn.boost.org/trac10/ticket/10009  
about 5 years ago, hoping **at least** to get back an authoritative answer and to engage in a constructive discussion, while I got simply **nothing** back.  
  
I knew that shared object access is not safe, but I tried to explain why this design is flawed, at least for some uses cases, and that the library should provide at least a way to make the shared access to objects be thread-safe (like in the patch I sent) using a compile-time flag (a #define).   
  
In conclusion, I'm not sad because I did not get my patch merged or something like that: it's fine to stick with some, even bad, ideas when you own a project, because you believe you're right.  
I'm sad because my pretty reasonable argumentation did not even deserve an answer, for 5 years. And the **saddest thing here** is that I'm pretty sure that a lot of people using this library, are using it *unsafely* (because it has a misleading design and a not-so-good-documentation) and that most of the time they're *unable to realize that*: the race condition is so **subtle** that, depending on the use cases, might requires a certain "scale" in order to find the bug. It's a totally non-obvious and nasty problem to find. Nothing like what happens when an operation is 100% unsafe [obvious, often-reproducible crash]. Here, most of the operations are *unfortunately* 100% safe, while some operations like `close()` are safe something like 99% of the time and **that's terrible**, because it reduces by a lot the likelihood of a memory corruption, leading to a crash.  
  
Note: all I know is about Asio in Boost 1.55, which is old, of course. But I suspect that most of the stuff still applies to Boost 1.71.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:03:35 UTC  
> Url: https://github.com/boostorg/asio/issues/229#issuecomment-752291967  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#686](https://github.com/chriskohlhoff/asio/issues/686).
