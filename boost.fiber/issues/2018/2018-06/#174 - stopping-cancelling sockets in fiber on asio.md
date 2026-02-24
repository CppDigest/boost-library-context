# #174 - stopping/cancelling sockets in fiber on asio [Closed]

> Username: romange  
> Created at: 2018-06-03 13:15:54 UTC  
> Updated at: 2018-12-22 05:16:48 UTC  
> Closed at: 2018-12-22 05:16:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/174  

Hi Oliver,  
I am playing with your fiber with asio example but I added few features:  
1. `server(...)` function tracks all the open connections using intrusive::list of Connection objects that contain  tcp::socket.   
2. `session(Connection*)` function unlinks the connection object from the list upon exit.  
That part seems to work.  
  
In order to allow correct shutdown of all the resources of the server (fibers/sockets) I have the following code:  
  
```  
void server(const std::shared_ptr<asio::io_service>& io_svc, tcp::acceptor & a) {  
 ConnectionList clist;  
  
/* Acceptor loop as in example + adding new connections to clist   
 .....  
*/  
 // After acceptor loop.  
 for (auto it = clist.begin(); it != clist.end(); ++it) {  
      it->socket->cancel();  
      it->socket->close();  
 }  
 LOG(INFO) << "Waiting for connections to close";  
  
  while (!clist.empty()) {  
      this_fiber::yield();    // Deadlock here!!!  
   }  
  io_svc->stop();  
```  
Which should close all the sockets and waits for all session fibers to exit. However I do not see that session fibers become active and continue after async_read_some call.  It seems that `socket->cancel` or `close` do not have any effect on the socket in that case. I know that it should work because you do similar thing in   
`cpp03/http/server/connection_manager.cpp` but it does not work with fibers.  
  
Am I missing anything?

---

## Comment 1

> Username: romange  
> Created at: 2018-06-03 13:35:52 UTC  
> Url: https://github.com/boostorg/fiber/issues/174#issuecomment-394162912  

It seems that the io service fiber loop is stuck at `cnt_.wait()` line even when the active fiber yields itself.   
If `this_fiber::yield()` does not pass the execution to io_service loop fiber how can I pass it?  
```  
while ( ! io_svc_->stopped() ) {  
        if ( has_ready_fibers() ) {  
            // run all pending handlers in round_robin  
            while ( io_svc_->poll() );  
            // block this fiber till all pending (ready) fibers are processed  
            // == round_robin::suspend_until() has been called  
            std::unique_lock< fibers::mutex > lk( mtx_);  
            LOG(INFO) << "Before CndWait";  
            cnd_.wait(lk);  
            LOG(INFO) << "After CndWait";  
        } else {  
            // run one handler inside io_service  
            // if no handler available, block this thread  
  
            if ( ! io_svc_->run_one() ) {  
                break;  
            }  
        }  
      }  
```

---

## Comment 2

> Username: romange  
> Created at: 2018-06-03 13:51:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/174#issuecomment-394163847  

Ok, I think I understand what happens. in   
```  
while (!clist.empty()) {  
      this_fiber::yield();   
   }  
```  
`yield` does not qualify for thread suspension and the scheduler just calls the same fiber again and again without calling suspend_until and releasing the thread for io_service processing.   
  
So, what's the idiomatic way to tell the scheduler to jump back to io_service loop?   
Can I somehow notify the `round_robin.cnd_`  directly or should I introduce condition_variable just for suspending the calling fiber?
