# #251 - BOOT_ASSERT failure on Visual Studio Debug build (<boost>/lib/fiber/src/scheduler.cpp:406). [Closed]

> Username: yystju  
> Created at: 2020-06-21 13:33:26 UTC  
> Updated at: 2020-10-15 16:18:31 UTC  
> Closed at: 2020-10-14 14:36:19 UTC  
> Url: https://github.com/boostorg/fiber/issues/251  

Hi,  
  
I have been using boost::fiber in my code for several weeks now. Before today it works very well. But this morning, I run into an BOOT_ASSERT failure which occurred at <boost>/lib/fiber/src/scheduler.cpp:406. For now, I think I've tried all what I can do ... from building boosts again to comment all the content of the coroutine functions... At last, I found the official example "work_stealing.cpp" cannot work as well... I am guessing it's related to the updating of my windows10 this morning... But I have no direct evidence.  Please take a look, if there're other guys who also run into the situation...  
  
Thank you.  
  
-Quan  
  
The boost code is like below:  
```  
// <boost>/lib/fiber/src/scheduler.cpp  
...  
void  
scheduler::detach_worker_context( context * ctx) noexcept {  
    BOOST_ASSERT( nullptr != ctx);  
    BOOST_ASSERT( ! ctx->ready_is_linked() );  
#if ! defined(BOOST_FIBERS_NO_ATOMICS)  
    BOOST_ASSERT( ! ctx->remote_ready_is_linked() );  
#endif  
    BOOST_ASSERT( ! ctx->sleep_is_linked() );  
    BOOST_ASSERT( ! ctx->terminated_is_linked() );  
    BOOST_ASSERT( ! ctx->wait_is_linked() ); // <-- [THE ERROR RAISED FROM HERE!]  
    BOOST_ASSERT( ctx->worker_is_linked() );  
    BOOST_ASSERT( ! ctx->is_context( type::pinned_context) );  
    ctx->worker_unlink();  
    BOOST_ASSERT( ! ctx->worker_is_linked() );  
    ctx->scheduler_ = nullptr;  
    // a detached context must not belong to any queue  
}  
...  
```  
My code is like (I removed the not-related parts, but it's based on the "work_stealing.cpp"):  
```  
//coroutine.h  
class CoroutineManager {  
  ...  
  
  typedef std::unique_lock<boost::fibers::mutex> lock_type;  
  typedef boost::fibers::algo::work_stealing scheduler_algorithm;  
  typedef boost::fibers::default_stack stack_type;  
    
  ...  
  void wait() {  
    lock_type shutdown_lock(shutdown_mutex);  
    shutdown_condition.wait(shutdown_lock);  
  }  
  
  void shutdown(bool wait = true) {  
    lock_type shutdown_lock(shutdown_mutex);  
  
    shutdown_lock.unlock();  
  
    this->shutdown_condition.notify_all();  
  
    if (wait) {  
      for (std::thread& t : threads) {  
        if (t.joinable()) {  
          t.join();  
        }  
      }  
    }  
  }  
    
  ...  
  
  template <class TaskType>  
  void submit(const TaskType& task) {  
    boost::fibers::fiber(boost::fibers::launch::dispatch, std::allocator_arg, stack_type(this->stack_size), task).detach();  
  }  
    
  ...  
    
  void init() {  
    if (verbose) spdlog::info("INIT THREAD({0}) STARTED.", std::this_thread::get_id());  
  
    for (int i = 0; i < (this->thread_count - 1); ++i) {  
      threads.push_back(std::thread(&CoroutineManager::thread, this));  
    }  
  
    boost::fibers::use_scheduling_algorithm<scheduler_algorithm>(this->thread_count);  
  
    this->start_barrier.wait();  
  }  
  
  void dispose() {  
    if (verbose) spdlog::info("INIT THREAD({0}) DISPOSED.", std::this_thread::get_id());  
  }  
  
  void thread() {  
    if(verbose) spdlog::info("WORKER THREAD({0}) STARTED.", std::this_thread::get_id());  
  
    boost::fibers::use_scheduling_algorithm<scheduler_algorithm>(this->thread_count);  
  
    this->start_barrier.wait();  
  
    this->wait();  
  
    if (verbose) spdlog::info("WORKER THREAD({0}) DISPOSED.", std::this_thread::get_id());  
  }  
  
  ...  
};  
  
- - - - - - - - - - - - - - - - - - - - - - -  
  
// coroutine_test.cpp  
...  
  
int main(int argc, char* argv[]) {  
  init_file_logger("coroutine_test.log");  
  time_tracker tracker("[coroutine_test.main]");  
  
  typedef std::thread::id tid_t;  
  typedef boost::fibers::fiber::id fid_t;  
  typedef boost::fibers::buffered_channel<std::tuple<tid_t, fid_t, int>> channel_t;  
  
  CoroutineManager manager(std::thread::hardware_concurrency(), 1024 * 1024 * 8, true);  
  
  const int N = 1;  
  
  channel_t chan { 8 };  
  
  boost::fibers::barrier done_flag(2);  
  
  manager.submit([&chan, &done_flag]() {  
    std::tuple<tid_t, fid_t, int> p;  
  
    while( boost::fibers::channel_op_status::success == chan.pop_wait_for(p, std::chrono::milliseconds(100))) {  
      spdlog::info("[thead({0}) : fiber({1}) from {2}]", std::get<0>(p), std::get<1>(p), std::get<2>(p));  
    }  
  
    done_flag.wait();  
  });  
  
  for (int i = 0; i < N; ++i) {  
    manager.submit([&chan, i]() {  
      for (int j = 0; j < 1000; ++j) {  
        chan.push(std::move(std::make_tuple(std::this_thread::get_id(), boost::this_fiber::get_id(), i)));  
        boost::this_fiber::yield();  
      }  
    });  
  }  
  
  done_flag.wait();  
  
  spdlog::info("-START TO SHUTDOWN-");  
  
  manager.shutdown();  
  
  spdlog::info("-END-");  
  
  return 0;  
}  
  
```  
  
Please see the attached snapshot for what happened...  
![error](https://user-images.githubusercontent.com/480971/85225873-b5231480-b406-11ea-809e-9adebff797fb.png)

---

## Comment 1

> Username: yystju  
> Created at: 2020-06-21 13:41:55 UTC  
> Url: https://github.com/boostorg/fiber/issues/251#issuecomment-647130132  

Oh, I forgot to mention:   
  
1) I am using cmake to manage the build. I found on windows Release build, the code can work very well. On WSL(Windows Subsystem Linux) Ubuntu20.04, It can work as well on both Release and Debug build. I haven't tried macOS, but, yestoday, the code can work on it...  
  
2) My code is based on boot 1.73.0

---

## Comment 2

> Username: dmitryikh  
> Created at: 2020-10-15 16:18:30 UTC  
> Url: https://github.com/boostorg/fiber/issues/251#issuecomment-709436146  

Hello, @yystju !  
  
Can you please check does it work with last fixes in `develop` branch?  
  
thanks!
