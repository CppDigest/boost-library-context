# #300 - Should epoll_reactor::run lock descriptors? [Closed]

> Username: bmerry  
> Created at: 2019-11-13 06:35:24 UTC  
> Updated at: 2020-12-30 01:11:16 UTC  
> Closed at: 2020-12-30 01:11:15 UTC  
> Url: https://github.com/boostorg/asio/issues/300  

While digging through the Boost.Asio source to try to track down a spurious wakeup problem when using multiple threads (`async_read_some` with null_buffers calling a completion handler, but the handler getting EAGAIN when it tries to read) I noticed what might be a race condition in `epoll_reactor::run`. I'm by no means familiar with the rather complex internals of Boost.Asio and I haven't confirmed it experimentally, so I apologise if there is something I've missed.  
  
The code in question is [this](https://github.com/boostorg/asio/blob/b6c27b9c3167b9b7dd0a30de9dfe6f3aa33bdaf3/include/boost/asio/detail/impl/epoll_reactor.ipp#L538-L547). From what I could tell, `task_io_service` calls `epoll_reactor::run` without a lock, and hence multiple threads can enter this function simultaneously. They're each given a thread-local I/O ops_queue, which is spliced into the shared ops_queue in the cleanup, so the use of `ops` is safe. However, isn't `descriptor_data` potentially going to be the same across threads? Even though you're using EPOLLET, the following could (I think) happen:  
  
1. Some data arrives on the socket.  
2. Thread 1 is woken up from `epoll_wait`.  
3. Some more data arrives on the socket.  
4. Thread 2 is woken up from `epoll_wait`.  
5. Both threads progress to the indicated region of code, and both try to add the descriptor to their private operation queue.  
  
From epoll(7):  
> Since even with edge-triggered epoll, multiple events can be  generated upon  receipt  of multiple chunks of data, the caller has the option to specify the EPOLLONESHOT flag, to tell epoll to disable the  associated file descriptor after the receipt of an event with epoll_wait(2).

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:11:14 UTC  
> Url: https://github.com/boostorg/asio/issues/300#issuecomment-752293194  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#731](https://github.com/chriskohlhoff/asio/issues/731).
