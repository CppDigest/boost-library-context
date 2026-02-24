# #418 - Boost::Asio Asynchronous Mutex Implementation [Open]

> Username: WarrenN1  
> Created at: 2023-05-01 02:53:45 UTC  
> Updated at: 2023-07-29 07:38:13 UTC  
> Url: https://github.com/boostorg/asio/issues/418  

I had a question about the following asynchronous mutex I am working on:  
```  
#include <boost/asio.hpp>  
#include <boost/thread/mutex.hpp>  
#include <memory>  
  
class async_mutex {  
public:  
    // Lock the mutex asynchronously  
    boost::asio::awaitable<void> async_lock() {  
        // Define the lock_awaiter object that will handle suspension and resumption  
        class lock_awaiter {  
        public:  
            lock_awaiter(async_mutex& mutex) : m_mutex(mutex) {}  
  
            // Check if the mutex is locked  
            bool await_ready() const noexcept {  
                return m_mutex.m_mutex.try_lock();  
            }  
  
            // Suspend the coroutine and add it to the list of waiters  
            void await_suspend(std::coroutine_handle<> coroutine) {  
                m_mutex.m_waiters.emplace_back(coroutine);  
            }  
  
            // Unused, just to match the coroutine_traits definition  
            void await_resume() noexcept {}  
  
        private:  
            async_mutex& m_mutex;  
        };  
  
        // Return the lock_awaiter object  
        return lock_awaiter(*this);  
    }  
  
    // Unlock the mutex and resume the first waiting coroutine  
    void unlock() {  
        // Acquire a lock on the internal mutex to protect access to m_waiters  
        boost::mutex::scoped_lock lock(m_mutex);  
  
        // If there are waiting coroutines, resume the first one in the queue  
        if (!m_waiters.empty()) {  
            auto coroutine = m_waiters.front();  
            m_waiters.pop_front();  
            coroutine.resume();  
        } else {  
            m_mutex.unlock();  
        }  
    }  
  
private:  
    // Deque of coroutines waiting for the mutex to be released  
    std::deque<std::coroutine_handle<>> m_waiters;  
  
    // Internal mutex used to protect access to m_waiters  
    boost::mutex m_mutex;  
};  
```  
The goal of this is to be able to use mutexes on an io_ctx that uses multiple threads that may have to contend for shared resources. I think the above is correct. My primary concern is that in my await_ready() try_lock's behavior is technically undefined for a thread that already owns the mutex. In a scenario where say Thread1 is running async(f1()) who owns the mutex and suspends for some other reason, if Thread1 upon suspensions picks up async(f2()) which tries to acquire the mutex this could result in undefined behavior.

---

## Comment 1

> Username: ArielSilver  
> Created at: 2023-06-24 17:07:21 UTC  
> Url: https://github.com/boostorg/asio/issues/418#issuecomment-1605653957  

```  
#include <iostream>  
#include <boost/asio.hpp>  
#include <boost/thread.hpp>  
class AsyncMutex {  
public:  
 AsyncMutex(boost::asio::io_context& ioContext)  
 : strand_(ioContext), locked_(false) {}  
 template <typename Handler>  
 void lock(Handler&& handler) {  
 strand_.post([this, handler = std::move(handler)]() mutable {  
 if (!locked_) {  
 locked_ = true;  
 handler(boost::system::error_code());  
 } else {  
 waitingTasks_.emplace_back(std::move(handler));  
 }  
 });  
 }  
 void unlock() {  
 strand_.post([this]() {  
 if (waitingTasks_.empty()) {  
 locked_ = false;  
 } else {  
 auto handler = std::move(waitingTasks_.front());  
 waitingTasks_.pop_front();  
 handler(boost::system::error_code());  
 }  
 });  
 }  
private:  
 boost::asio::io_context::strand strand_;  
 bool locked_;  
 std::list<std::function<void(boost::system::error_code)>>   
waitingTasks_;  
};  
int main() {  
 boost::asio::io_context ioContext;  
 AsyncMutex asyncMutex(ioContext);  
 // Create the first task  
 ioContext.post([&asyncMutex]() {  
 asyncMutex.lock([&asyncMutex](const   
boost::system::error_code&) {  
 std::cout << "Locked in thread: " << boost::this_thread::get_id()   
<< std::endl;  
 // Simulate some asynchronous operation  
   
boost::this_thread::sleep_for(boost::chrono::milliseconds(2000));  
 std::cout << "Unlocked in thread: " <<   
boost::this_thread::get_id() << std::endl;  
 asyncMutex.unlock();  
 });  
 });  
 // Create the second task  
 ioContext.post([&asyncMutex]() {  
 asyncMutex.lock([&asyncMutex](const   
boost::system::error_code&) {  
 std::cout << "Locked in thread: " << boost::this_thread::get_id()   
<< std::endl;  
 // Simulate some asynchronous operation  
   
boost::this_thread::sleep_for(boost::chrono::milliseconds(3000));  
 std::cout << "Unlocked in thread: " <<   
boost::this_thread::get_id() << std::endl;  
 asyncMutex.unlock();  
 });  
 });  
 // Run the ioContext to start executing the tasks  
 ioContext.run();  
 return 0;  
}  
```  
  
Explanation:  
We define the AsyncMutex class that provides an   
asynchronous mutex implementation using   
Boost.Asio.  
The lock method takes a handler as a parameter   
and posts a task to the strand_ of the   
ioContext. If the mutex is not locked, it sets the   
locked_ flag to true and invokes the handler   
immediately. Otherwise, it adds the handler to the   
waitingTasks_ list for execution when the   
mutex is unlocked.  
The unlock method posts a task to the strand_  
of the ioContext that checks if there are any   
waiting tasks in the waitingTasks_ list. If there   
are, it removes the first task from the list and   
invokes its handler. If there are no waiting tasks, it   
sets the locked_ flag to false.  
In the main function, we create an instance of   
AsyncMutex and an ioContext.  
We create two tasks by using the   
ioContext.post function and capturing the   
AsyncMutex instance by reference.  
Each task calls the lock method on the   
asyncMutex and provides a lambda function as   
the handler

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-07-29 07:38:13 UTC  
> Url: https://github.com/boostorg/asio/issues/418#issuecomment-1656586898  

This compiles?   
  
Either way, maybe take a look at [sam](https://github.com/klemens-morgenstern/sam).  
  
I don't think that library will end up boost however, but feel free to copy & paste.
