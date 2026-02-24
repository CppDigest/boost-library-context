# #320 - epoll_reactor occasionally misses wakeup on Linux >= 5.3 [Closed]

> Username: neunhoef  
> Created at: 2020-01-21 10:35:49 UTC  
> Updated at: 2020-12-30 01:12:34 UTC  
> Closed at: 2020-12-30 01:12:33 UTC  
> Url: https://github.com/boostorg/asio/issues/320  

I have observed a wakeup problem in a large server application (https://arangodb.com) which uses `boost::asio`. A completion handler which is posted to an `io_context` is not executed in a timely fashion. The `io_context` runs only a single thread. The problem seems to occur under Linux >= 5.3 only.  
  
I have actually tracked down the problem to what I think is a problem in the Linux kernel. See [this link for the bug report](https://bugzilla.kernel.org/show_bug.cgi?id=205933). Unfortunately, there was no response to this in over a month now, so we might have to work around this in `boost::asio` or even directly in ArangoDB.  
  
Here is the background: `boost::asio` uses an `eventfd` to wake up an `epoll_loop` when a new completion handler is posted to the `io_context`. Furthermore, edge-based wakeup is used, so that the event fires only once. Once the completion handler is posted to the `io_context`, to actually wake up the `epoll`, the code at: https://github.com/boostorg/asio/blob/95f7e936356810addf3a6b54dbd1129d10f3ba98/include/boost/asio/detail/impl/epoll_reactor.ipp#L568 is used, which uses `epoll_ctl` to readd the `eventfd` to the `epoll` file descriptor in the hope that this resets the edge detection and thus wakes up the file descriptor, since the `eventfd` is permanently kept in a readable state.  
  
This seems to work beautifully for Linux < 5.3 according to my experiments. However, with both an Ubuntu 5.3 Linux kernel and a vanilla 5.3 Linux kernel very occasional lost wakeups can be observed. I have produced a program (not using `boost::asio`) which shows the problem, it is attached to the above Linux bug report.  
  
If the Linux kernel folks do not answer or indeed are of the opinion that this method is not actually supposed to work reliably (I did not find documentation about this), then we might have to work around this problem in `boost::asio`. The following diff solves the problem for me, but might have an impact on performance:  
  
```  
boost/libs/asio/include/boost/asio/detail/impl/epoll_reactor.ipp  
--- a/boost/libs/asio/include/boost/asio/detail/impl/epoll_reactor.ipp  
+++ b/boost/libs/asio/include/boost/asio/detail/impl/epoll_reactor.ipp  
@@ -512,10 +512,16 @@ void epoll_reactor::run(long usec, op_queue<operation>& ops)  
     void* ptr = events[i].data.ptr;  
     if (ptr == &interrupter_)  
     {  
+      // Currently obsolete:  
       // No need to reset the interrupter since we're leaving the descriptor  
       // in a ready-to-read state and relying on edge-triggered notifications  
       // to make it so that we only get woken up when the descriptor's epoll  
       // registration is updated.  
+      //  
+      // The above is the original comment. We have to work around a bug in  
+      // Linux 5.3 and thus are using the actual interrupt() call of the  
+      // interrupter. Therefore, we have to reset it here:  
+      interrupter_.reset();  
   
 #if defined(BOOST_ASIO_HAS_TIMERFD)  
       if (timer_fd_ == -1)  
@@ -567,10 +573,19 @@ void epoll_reactor::run(long usec, op_queue<operation>& ops)  
   
 void epoll_reactor::interrupt()  
 {  
+  // The following code does not work correctly on Linux 5.3, since sometimes  
+  // wakeup events are lost, when the event comes in at exactly the same time  
+  // as the epoll_wait call happens. Therefore we are using the official  
+  // interrupt() method here, and correspondingly reset() the interrupt when  
+  // we have been woken up in epoll_reactor::run.  
+#if 0  
   epoll_event ev = { 0, { 0 } };  
   ev.events = EPOLLIN | EPOLLERR | EPOLLET;  
   ev.data.ptr = &interrupter_;  
   epoll_ctl(epoll_fd_, EPOLL_CTL_MOD, interrupter_.read_descriptor(), &ev);  
+#else  
+  interrupter_.interrupt();  
+#endif  
 }  
   
 int epoll_reactor::do_epoll_create()  
```  
  
What do you guys think about this topic and how could it be resolved for us users of `boost::asio`? Do you have any contacts in the Linux world to resolve this problem?  
  
Cheers,  
  Max.

---

## Comment 1

> Username: wegylexy  
> Created at: 2020-04-19 02:39:02 UTC  
> Url: https://github.com/boostorg/asio/issues/320#issuecomment-616012040  

Are you sure it does not happen in Linux 4.18? I have a program that hangs at `epoll_wait()` every several hours until it is killed with SIGINT, and spent months trying to dig out way.

---

## Comment 2

> Username: geordieintheshellcode  
> Created at: 2020-05-29 11:36:54 UTC  
> Url: https://github.com/boostorg/asio/issues/320#issuecomment-635925306  

I can confirm that we've seen the same issue running on 5.5.5-200.fc31.x86_64 and the suggested patch does fix the issue.

---

## Comment 3

> Username: wegylexy  
> Created at: 2020-07-08 07:01:16 UTC  
> Url: https://github.com/boostorg/asio/issues/320#issuecomment-655328988  

Until the Linux kernel is fixed, should it default to disable `eventfd`?

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:12:32 UTC  
> Url: https://github.com/boostorg/asio/issues/320#issuecomment-752293370  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#742](https://github.com/chriskohlhoff/asio/issues/742).
