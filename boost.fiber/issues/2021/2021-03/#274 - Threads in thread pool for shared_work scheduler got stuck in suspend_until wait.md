# #274 - Threads in thread pool for shared_work scheduler got stuck in suspend_until wait [Open]

> Username: monazd  
> Created at: 2021-03-11 22:00:21 UTC  
> Updated at: 2021-03-11 22:00:21 UTC  
> Url: https://github.com/boostorg/fiber/issues/274  

I am writing a test to test shared_work scheduler with buffered_channel based on work_sharing.cpp example.  
  
In the test, I have a thread pool with 8 threads, all of them are running shared_worker scheduler with suspend = True. There are two buffered channels to do synchronization between fibers  
  
Two fibers are pushing input into channel 1 like this.  
```C++  
boost::fibers::fiber{[&chan1] {  
      
    int i = 0;  
    int counter{0};  
    while (true) {  
      char item = (i++%26)+'a';  
      chan1.push(item);  
      std::cout << "pushed " << item << " into pipeline" << std::endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      if (++counter == 1024) {  
        counter = 0;  
        boost::this_fiber::sleep_for(std::chrono::milliseconds(10+rand()%10));  
      }  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "feeder 1 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }}.detach();  
```  
Four Fibers to pop from buffer channel 1 , do some CPU intensive task and push item into buffer channel 2 like this  
``` C++  
boost::fibers::fiber([&chan1, &chan2]() {  
      
    while (true) {  
      char item;  
      chan1.pop(item);  
      cout << "stage 1:2 consumed " << item << endl;  
      do_primes();  
      chan2.push(item);  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 1:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
   }).detach();  
```  
Another four fibers to pop from buffer channel 2 and do some CPU intensive task like this  
``` C++  
boost::fibers::fiber([&chan2]() {  
      
    while (true) {  
      char item;  
      chan2.pop(item);  
      do_primes1();  
      cout << "stage 2:1 consumed " << item << endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 2:1 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }).detach();   
```  
  
When running the test on 8 core machine,  I observed that some worker threads in thread pool are never running.   
![image](https://user-images.githubusercontent.com/31806934/110858748-4e34dc80-826f-11eb-9eb8-910d37b10688.png)  
  
And when I gdb to look into stuck thread, it looks like they are blocked by wait in suspend_until  
![image](https://user-images.githubusercontent.com/31806934/110859034-ab309280-826f-11eb-9007-c9b2bfd48acc.png)  
  
Each time I run the test, the number of threads that are stuck is random. I also try the test without buffered channel, the random disappears and everything seems perfect.  
I have run the test on both libboost-fiber-1.71 and newest relase 1.75. The issue is still there.  
  
Is there a race condition for conditional_variable in scheduler and buffered channel that may cause this issue? Is there any workaround to avoid it?  
Thank you!  
  
Here is the complete test code to reproduce the issue  
```  
//          Copyright Nat Goodspeed + Oliver Kowalke 2015.  
// Distributed under the Boost Software License, Version 1.0.  
//    (See accompanying file LICENSE_1_0.txt or copy at  
//          http://www.boost.org/LICENSE_1_0.txt)  
  
#include <chrono>  
#include <string>  
#include <cstdlib>  
#include <condition_variable>  
#include <cstddef>  
#include <deque>  
#include <iomanip>  
#include <iostream>  
#include <mutex>  
#include <sstream>  
#include <string>  
#include <thread>  
#include <boost/fiber/buffered_channel.hpp>  
#include <boost/fiber/all.hpp>  
#include <boost/fiber/detail/thread_barrier.hpp>  
#include <boost/assert.hpp>  
  
static std::size_t fiber_count{ 0 };  
static std::mutex mtx_count{};  
static boost::fibers::condition_variable_any cnd_count{};  
typedef std::unique_lock<std::mutex > lock_type;  
using boost::fibers::detail::thread_barrier;  
  
const int MAX_PRIME = 10000;  
  
void do_primes()  
{  
    unsigned long i, num, primes = 0;  
    for (num = 1; num <= MAX_PRIME; ++num) {  
        for (i = 2; (i <= num) && (num % i != 0); ++i);  
        if (i == num)  
            ++primes;  
    }  
    printf("Calculated %d primes 0.\n", primes);  
}  
  
  
void do_primes1()  
{  
    unsigned long i, num, primes = 0;  
    for (num = 1; num <= MAX_PRIME; ++num) {  
        for (i = 2; (i <= num) && (num % i != 0); ++i);  
        if (i == num)  
            ++primes;  
    }  
    printf("Calculated %d primes 1.\n", primes);  
}  
  
  
/*****************************************************************************  
*   example fiber function  
*****************************************************************************/  
  
/*****************************************************************************  
*   example thread function  
*****************************************************************************/  
  
void start_thread( thread_barrier * b, int i) {  
    std::ostringstream buffer;  
    std::string thread_name = std::string{"worker_thread_"} + std::to_string(i);  
    pthread_setname_np(pthread_self(), thread_name.c_str()); // set the name (pthread_self() returns the pthread_t of the current thread)  
    buffer << "thread started " << std::this_thread::get_id() << std::endl;  
    boost::fibers::use_scheduling_algorithm< boost::fibers::algo::shared_work>(true); /*<  
        Install the scheduling algorithm `boost::fibers::algo::shared_work` in order to  
        join the work sharing.  
    >*/  
    b->wait(); /*< sync with other threads: allow them to start processing >*/  
    boost::fibers::fiber test_fiber([] {  
      while(true) {  
        boost::this_fiber::sleep_for(std::chrono::seconds(1));  
      }  
    });  
    lock_type lk( mtx_count);  
    std::cout << buffer.str() << std::flush;  
    cnd_count.wait( lk, [](){return 0 == fiber_count;} ); /*<  
        Suspend main fiber and resume worker fibers in the meanwhile.  
        Main fiber gets resumed (e.g returns from `condition_variable_any::wait()`)  
        if all worker fibers are complete.  
    >*/  
    test_fiber.join();  
    BOOST_ASSERT( 0 == fiber_count);  
}  
//]  
  
/*****************************************************************************  
*   main()  
*****************************************************************************/  
using std::endl;  
using std::cout;  
int main( int argc, char *argv[]) {  
  //[main_ws  
   /*<  
Install the scheduling algorithm `boost::fibers::algo::shared_work` in the main  
thread too, so each new fiber gets launched into the shared pool.  
>*/  
  boost::fibers::use_scheduling_algorithm<boost::fibers::algo::shared_work>(true);  
  boost::fibers::buffered_channel<char> chan1(64);  
  boost::fibers::buffered_channel<char> chan2(64);  
  boost::fibers::fiber([&chan1, &chan2]() {   
      
    while (true) {  
      char item;  
      chan1.pop(item);  
      cout << "stage 1:1 consumed " << item << endl;  
      do_primes();  
      chan2.push(item);  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 1:1 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
   }).detach();  
   fiber_count ++;  
  boost::fibers::fiber([&chan1, &chan2]() {  
      
    while (true) {  
      char item;  
      chan1.pop(item);  
      cout << "stage 1:2 consumed " << item << endl;  
      do_primes();  
      chan2.push(item);  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 1:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
   }).detach();  
   fiber_count ++;   
   boost::fibers::fiber([&chan1, &chan2]() {   
      
    while (true) {  
      char item;  
      chan1.pop(item);  
      cout << "stage 1:3 consumed " << item << endl;  
      do_primes();  
      chan2.push(item);  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 1:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
   }).detach();  
   fiber_count ++;  
   boost::fibers::fiber([&chan1, &chan2]() {   
      
    while (true) {  
      char item;  
      chan1.pop(item);  
      cout << "stage 1:4 consumed " << item << endl;  
      do_primes();  
      chan2.push(item);  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 1:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
   }).detach();  
   fiber_count ++;  
  boost::fibers::fiber([&chan2]() {  
      
    while (true) {  
      char item;  
      chan2.pop(item);  
      do_primes1();  
      cout << "stage 2:1 consumed " << item << endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 2:1 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }).detach();  
  fiber_count ++;  
  boost::fibers::fiber([&chan2]() {  
      
    while (true) {  
      char item;  
      chan2.pop(item);  
      do_primes1();  
      cout << "stage 2:2 consumed " << item << endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 2:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }).detach();  
  fiber_count ++;  
  boost::fibers::fiber([&chan2]() {  
      
    while (true) {  
      char item;  
      chan2.pop(item);  
      do_primes1();  
      cout << "stage 2:3 consumed " << item << endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 2:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }).detach();  
  fiber_count ++;  
  boost::fibers::fiber([&chan2]() {  
      
    while (true) {  
      char item;  
      chan2.pop(item);  
      do_primes1();  
      cout << "stage 2:4 consumed " << item << endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "stage 2:2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }).detach();  
  fiber_count ++;  
  
  boost::fibers::fiber{[&chan1] {  
      
    int i = 0;  
    int counter{0};  
    while (true) {  
      char item = (i++%26)+'a';  
      chan1.push(item);  
      std::cout << "pushed " << item << " into pipeline" << std::endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      if (++counter == 1024) {  
        counter = 0;  
        boost::this_fiber::sleep_for(std::chrono::milliseconds(10+rand()%10));  
      }  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "feeder 1 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }}.detach();  
  fiber_count ++;  
  boost::fibers::fiber{[&chan1] {  
      
    int i = 0;  
    int counter{0};  
    while (true) {  
      char item = (i++%26)+'a';  
      chan1.push(item);  
      std::cout << "pushed " << item << " into pipeline" << std::endl;  
      auto prev_thread = std::this_thread::get_id();  
      boost::this_fiber::yield();  
      if (++counter == 1024) {  
        counter = 0;  
        boost::this_fiber::sleep_for(std::chrono::milliseconds(10+rand()%10));  
      }  
      auto after_thread = std::this_thread::get_id();  
      if (prev_thread != after_thread) {  
        std::cout << "feeder 2 migrated from " << prev_thread << " to "  
                  << after_thread << std::endl;  
      }  
    }  
    lock_type lk( mtx_count);  
    if ( 0 == --fiber_count) { /*< Decrement fiber counter for each completed fiber. >*/  
        lk.unlock();  
        cnd_count.notify_all(); /*< Notify all fibers waiting on `cnd_count`. >*/  
    }  
  }}.detach();  
  fiber_count ++;  
  thread_barrier b(8);  
  std::thread threads[] = {  
      std::thread(start_thread, &b, 1), std::thread(start_thread, &b, 5),  
      std::thread(start_thread, &b, 2), std::thread(start_thread, &b, 6),  
      std::thread(start_thread, &b, 3), std::thread(start_thread, &b, 7),  
      std::thread(start_thread, &b, 4)};  
  std::cout << "main thread started " << std::this_thread::get_id()  
            << std::endl;  
  b.wait(); /*< sync with other threads: allow them to start processing >*/  
  {  
    lock_type /*< `lock_type` is typedef'ed as __unique_lock__<  
                 [@http://en.cppreference.com/w/cpp/thread/mutex `std::mutex`] >  
                 >*/  
                  lk(mtx_count);  
    cnd_count.wait(lk, []() {return 0 == fiber_count;}); /*<  
       Suspend main fiber and resume worker fibers in the meanwhile.  
       Main fiber gets resumed (e.g returns from  
   `condition_variable_any::wait()`) if all worker fibers are complete.  
   >*/  
  }                                                        
  BOOST_ASSERT(0 == fiber_count);  
  for (std::thread &t : threads) { /*< wait for threads to terminate >*/  
    t.join();  
  }  
  //]  
  std::cout << "done." << std::endl;  
  return EXIT_SUCCESS;  
}  
```
