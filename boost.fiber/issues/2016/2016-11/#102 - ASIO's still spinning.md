# #102 - ASIO's still spinning [Closed]

> Username: xaqq  
> Created at: 2016-11-29 10:35:14 UTC  
> Updated at: 2022-09-18 14:39:32 UTC  
> Closed at: 2022-09-18 14:39:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/102  

Hello @olk   
  
In #101 I mentioned noticing some weird behavior under Valgrind.  
It turns out that the issue is not Valgrind related, and after much trouble, I managed to  
reproduce it in a simple example. Very similar to the one in #100.  
  
Consider the following program:  
  
````  
std::shared_ptr<boost::fibers::unbuffered_channel<int>> c;  
std::shared_ptr<boost::fibers::unbuffered_channel<int>> c2;  
  
void run_t1()  
{  
    std::atomic_thread_fence(std::memory_order_acquire);  
    auto io_ptr = std::make_shared<boost::asio::io_service>();  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_ptr);  
  
    io_ptr->post([&](){  
        for (int i = 0 ; i < 50 ; ++i)  
        {  
            std::stringstream ss;  
            ss << "Will push " << i << std::endl;  
            std::cout << ss.str();  
  
            c->push(i);  
            c2->value_pop(); //spin at this point, until t2 has pushed something.  
        }  
    });  
    io_ptr->run();  
}  
  
void run_t2()  
{  
    std::atomic_thread_fence(std::memory_order_acquire);  
    auto io_ptr = std::make_shared<boost::asio::io_service>();  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_ptr);  
  
    boost::fibers::fiber f([io_ptr](){  
        try {  
            for (;;) {  
                int i = c->value_pop();  
                std::cout << "Read " << i << " from channel." << std::endl;  
                std::this_thread::sleep_for(std::chrono::milliseconds(1000));  
                c2->push(i);  
            }  
        }  
        catch (const std::exception &e)  
        {  
            std::cout << "Caught: " << e.what() << std::endl;  
        }  
        io_ptr->post([io_ptr](){  
            io_ptr->stop();  
        });  
    });  
  
    io_ptr->run();  
}  
  
int main() {  
    c = std::make_shared<boost::fibers::unbuffered_channel<int>>();  
    c2 = std::make_shared<boost::fibers::unbuffered_channel<int>>();  
  
    std::atomic_thread_fence(std::memory_order_release);  
    std::thread t2(run_t2);  
  
    std::this_thread::sleep_for(std::chrono::milliseconds(2000));  
    std::thread t1(run_t1);  
  
    t2.join();  
    t1.join();  
    return 0;  
}  
````  
  
The program will spin at 100% cpu.  
I'm not sure we are supposed to initiate fiber-related operations (channel's `push()` / `pop()`) from a manually `post()`ed handler (as done in `run_t1()`), but I would expect we should be able to.  
  
I've tried to fix it in the `fibers::asio::round_robin()` algorithm, but I didn't managed to.  
What I've observed that may help fixing the issue:  
  + It seems `t1` thread is spinning when it calls `value_pop()`. It will spin untill the `this_thread::sleep()` call in `t2()` returns and a value is pushed (therefore `value_pop()` is able to complete).  
  + The spin in `value_pop()` seem to come from the infinite loop in the `scheduler::dispatch()`. `asio::round_robin::suspend_until()` is called, but return immediately, therefore spinning. It returns immediately because when doing `cdn_.notify_one()`, no one is actually waiting on `cnd_`, so the fiber context doesn't change, and `suspend_until()` is called in a loop.

---

## Comment 1

> Username: olk  
> Created at: 2016-11-29 12:34:33 UTC  
> Updated at: 2016-11-29 12:37:08 UTC  
> Url: https://github.com/boostorg/fiber/issues/102#issuecomment-263558882  

run_t1() posts a lambda to io_service that runs a for-loop of 50 iterations. the io_service of t1 wil return from io_service::run_one() (asio/round_robin.hpp:94) only after the 500 loops.  
the main-fiber of t1 (== t1's stack) is suspended in c2->value_pop() till a message arrives from t2.  
while the main-fiber is suspended the dispatcher-fiber (for maintenance) runs. because no fibers are ready, the dispatcher-fiber enters asio/round_robin::suspend_until(). in suspend_until() the dispatcher does not block (as in the ordinary round_robin impl.) because potentialy other task might be pending in io_service - so it returns but enters suspend_until() immediately (no ready fibers) -> 100% cpu usage.  
  
after returning form the for-loop the lambda terminates and io_service of t1 returns from io_service::run_one() inside asio/round_robin. you don't see a 100% cpu usage.  
  
the asio-integration is problematic (therefore in examples directory) - you have to synchronize two event dispatcher (fiber scheduler and asio's io_service). I believe it requires support by the asio implementation. otherwise it will get hard to be responsive and to prevent blocking as well as cpu burning.

---

## Comment 2

> Username: xaqq  
> Created at: 2016-11-30 19:22:48 UTC  
> Url: https://github.com/boostorg/fiber/issues/102#issuecomment-263968543  

Thank you for your explanation. It clears things up.  
  
I guess we'll have to wait for you boost developers to work your magic and implement proper support :)

---

## Comment 3

> Username: olk  
> Created at: 2016-11-30 19:40:03 UTC  
> Url: https://github.com/boostorg/fiber/issues/102#issuecomment-263973128  

you are welcome to provide fixes :^D

---

## Comment 4

> Username: vinipsmaker  
> Created at: 2017-11-23 22:34:40 UTC  
> Url: https://github.com/boostorg/fiber/issues/102#issuecomment-346705216  

> the asio-integration is problematic (therefore in examples directory) - you have to synchronize two event dispatcher (fiber scheduler and asio's io_service). I believe it requires support by the asio implementation. otherwise it will get hard to be responsive and to prevent blocking as well as cpu burning.  
  
So it is unlikely it'll hit fiber repository? Okay.

---

## Comment 5

> Username: olk  
> Created at: 2017-11-24 06:49:18 UTC  
> Url: https://github.com/boostorg/fiber/issues/102#issuecomment-346754646  

I'm too busy, but if someone is willing to provide the patches ...
