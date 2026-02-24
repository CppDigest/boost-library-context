# #443 - thread_pool::wait doesn't appear to do what it says it does [Open]

> Username: tsr-boxbot  
> Created at: 2025-02-25 02:07:09 UTC  
> Updated at: 2025-04-14 15:14:03 UTC  
> Url: https://github.com/boostorg/asio/issues/443  

Hi, I'm trying to understand the behavior of `thread_pool::wait`, the documentation (Boost 1.83) says:  
  
    This function blocks until the threads in the pool have completed. If stop() is not called prior to wait(), the wait() call will wait until the pool has no more outstanding work.  
  
Source: https://www.boost.org/doc/libs/1_83_0/doc/html/boost_asio/reference/thread_pool/wait.html  
  
If I have something like:  
  
```cpp  
boost::asio::thread_pool thread_pool(10);  
...  
boost::asio::post(thread_pool_, task0);  
....  
thread_pool.wait(); //stop was not called so should just wait until the pool has no more outstanding work.  
boost::asio::post(thread_pool_, task9999); // hangs forever  
```  
  
Since I didn't call `stop` I would expect to be able to post more things to the pool but it appears `wait` actually shutdowns the pool even if `stop` was not called.

---

## Comment 1

> Username: Chaosvex  
> Created at: 2025-04-14 15:06:38 UTC  
> Updated at: 2025-04-14 15:14:02 UTC  
> Url: https://github.com/boostorg/asio/issues/443#issuecomment-2802029212  

I wasn't able to reproduce the hang with your snippet but newer documentation states that `wait` and `join` are synonymous, so as you suspected, it's really just shutting the pool down gracefully, allowing any pending work to be completed first. I'd assume it's trying to be an equivalent to `std::thread::join()`, although the semantics of a thread and a thread pool don't quite match up, so it'd probably be an improvement to document that it also stops the scheduler.  
  
Behind the scenes, Asio is doing this on a join/wait:  
```cpp  
  
void thread_pool::join()  
{  
  if (joinable_)  
  {  
    joinable_ = false;  
    scheduler_.work_finished();  
    threads_.join();  
  }  
}  
  
// threads_join():  
  
// Wait for all threads in the group to exit.  
void join()  
{  
  while (first_)  
  {  
    first_->thread_.join();  
    item* tmp = first_;  
    first_ = first_->next_;  
    delete tmp;  
  }  
}  
```  
  
There's also an implicit join/wait if you allow the thread_pool to go out of scope, similar to `std::jthread`.  
  
If you're trying to submit work and want to wait for completion, you might be better off with `io_context::run_one`.
