# #194 - fcontext can't work in mips64 [Closed]

> Username: pkngpkng  
> Created at: 2022-01-13 03:02:53 UTC  
> Updated at: 2023-03-09 12:28:35 UTC  
> Closed at: 2023-01-26 05:58:14 UTC  
> Url: https://github.com/boostorg/context/issues/194  

* source  
  
``` cpp  
#include <boost/fiber/all.hpp>  
#include <chrono>  
#include <functional>  
#include <memory>  
#include <thread>  
  
class ThreadExecutor {  
public:  
  ThreadExecutor()  
      : m_channel(std::make_unique<  
                  boost::fibers::buffered_channel<std::function<void()>>>(  
            s_stackMaxCount)) {  
    std::promise<void> result;  
    m_thread = std::make_unique<std::thread>([this, &result] {  
      auto allocator = boost::fibers::pooled_fixedsize_stack(s_stackSize);  
  
      result.set_value();  
      std::function<void()> task;  
      while (boost::fibers::channel_op_status::closed != m_channel->pop(task)) {  
        --m_count;  
        boost::fibers::fiber{  
            std::allocator_arg, allocator,  
            [task = std::move(task), stackSize = s_stackSize]() { task(); }}  
            .detach();  
      }  
    });  
    result.get_future().get();  
  }  
  
  ~ThreadExecutor() noexcept {  
    if (!m_channel->is_closed()) {  
      m_channel->close();  
    }  
    if (m_thread->joinable()) {  
      m_thread->join();  
    }  
  }  
  
  void Stop() {  
    if (!m_channel->is_closed()) {  
      m_channel->close();  
    }  
  }  
  
  void Join() {  
    if (m_thread->joinable()) {  
      m_thread->join();  
    }  
  }  
  
  void PostTask(const std::function<void()> &task) {  
    const auto ret = m_channel->try_push(task);  
    switch (ret) {  
    case boost::fibers::channel_op_status::success:  
      /* likely path */  
      ++m_count;  
      break;  
    case boost::fibers::channel_op_status::full:  
      break;  
    case boost::fibers::channel_op_status::closed:  
      break;  
    default:  
      return;  
    }  
    return;  
  }  
  
private:  
  const size_t s_stackSize = 256 * 1024;  
  const size_t s_stackMaxCount = 1024;  
  std::unique_ptr<boost::fibers::buffered_channel<std::function<void()>>>  
      m_channel;  
  std::atomic_int m_count;  
  std::unique_ptr<std::thread> m_thread;  
};  
  
int main() {  
  ThreadExecutor executor;  
  
  std::promise<void> p1;  
  std::promise<void> p2;  
  printf("fiber task post \r\n");  
  executor.PostTask([&] {  
    printf("fiber task1 start \r\n");  
    boost::fibers::promise<void> promise;  
    std::thread t([&] {  
      printf("std thread1 task start \r\n");  
      std::this_thread::sleep_for(std::chrono::seconds(5));  
      printf("std thread1 task stop \r\n");  
      promise.set_value();  
    });  
    printf("fiber task1 sleep \r\n");  
    promise.get_future().get();  
    printf("fiber task1 stop \r\n");  
    p1.set_value();  
  });  
  executor.PostTask([&] {  
    printf("fiber task2 start \r\n");  
    boost::fibers::promise<void> promise;  
    std::thread t([&] {  
      printf("std thread task2 start \r\n");  
      std::this_thread::sleep_for(std::chrono::seconds(5));  
      printf("std thread task2 stop \r\n");  
      promise.set_value();  
    });  
    printf("fiber task2 sleep \r\n");  
    promise.get_future().get();  
    printf("fiber task2 stop \r\n");  
    p2.set_value();  
  });  
  
  p1.get_future().get();  
  p2.get_future().get();  
  return 0;  
}  
  
```  
  
* result  
  
``` text  
root@debian-mips64el:~# ./test_fiber   
fiber task post   
fiber task1 start   
std thread1 task start   
fiber task1 sleep   
fiber task2 start   
fiber task2 sleep   
[ 3716.428377] do_page_fault(): sending SIGSEGV to test_fiber for invalid read access from 0000000000000010  
[ 3716.436963] epc = 0000000000000011 in test_fiber[aaab270000+51000]  
[ 3716.439123] ra  = 0000000000000011 in test_fiber[aaab270000+51000]  
std thread task2 start   
Segmentation fault  
```  
  
It will crash at `promise.get_future().get();` returns;

---

## Comment 1

> Username: olk  
> Created at: 2022-01-13 06:44:06 UTC  
> Url: https://github.com/boostorg/context/issues/194#issuecomment-1011842545  

MIPS64 support was provided by other developers - I do not have access to this platform.  
It would be nice if you could provide a fix via pull-request.

---

## Comment 2

> Username: olk  
> Created at: 2023-01-26 05:58:13 UTC  
> Updated at: 2023-01-26 05:58:43 UTC  
> Url: https://github.com/boostorg/context/issues/194#issuecomment-1404591095  

should work now, see regression tests for mips64el on buster

---

## Comment 3

> Username: pkngpkng  
> Created at: 2023-03-09 12:28:34 UTC  
> Url: https://github.com/boostorg/context/issues/194#issuecomment-1461955890  

@olk I test the same example with boost 1.81.0 in Loongson-3A4000  
  
It will crash, and same output.
