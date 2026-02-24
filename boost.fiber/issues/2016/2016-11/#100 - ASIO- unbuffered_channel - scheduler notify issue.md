# #100 - ASIO: unbuffered_channel / scheduler notify issue [Closed]

> Username: xaqq  
> Created at: 2016-11-24 14:57:09 UTC  
> Updated at: 2016-11-27 10:43:16 UTC  
> Closed at: 2016-11-27 10:43:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/100  

Hello,  
  
I've spent more time trying to use both `asio` and `fiber` together. I am now aware that proper integration is still pending support in `asio`.  
  
However, I've faced a few issues that made the combined use of `asio` and `fiber` not usable (for my use case).  
  
----  
  
Consider the following test program:  
  
```  
std::shared_ptr<boost::fibers::unbuffered_channel<int>> c;  
  
void run_t1()  
{  
    auto io_ptr = std::make_shared<boost::asio::io_service>();  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_ptr);  
  
    boost::fibers::fiber f([](){  
        for (int i = 0; i < 10; ++i) {  
            std::cout << "Will push " << i << std::endl;  
            c->push(i);  
        }  
    });  
  
    f.detach();  
    io_ptr->run();  
}  
  
void run_t2()  
{  
    auto io_ptr = std::make_shared<boost::asio::io_service>();  
    boost::fibers::use_scheduling_algorithm<boost::fibers::asio::round_robin>(io_ptr);  
  
    boost::fibers::fiber f([](){  
        try {  
            for (;;) {  
                int i = c->value_pop();  
                std::cout << "Read " << i << " from channel." << std::endl;  
            }  
        }  
        catch (const std::exception &e)  
        {  
            std::cout << "Caught: " << e.what() << std::endl;  
        }  
    });  
  
    f.detach();  
    io_ptr->run();  
}  
  
int main() {  
    c = std::make_shared<boost::fibers::unbuffered_channel<int>>();  
  
    std::thread t1(run_t1);  
    std::thread t2(run_t2);  
  
    t2.join();  
    t1.join();  
    return 0;  
}  
```  
  
The expected output would be for the fiber in `run_t2` to display number ranging from 0 to 9. However, in my test, the output was either:  
   + No number (`value_pop()` never returned, not even once).  
  + Read number `0` and/or `1`.  
  
It never managed to read the 10 numbers from the channel. I again tried to dive into the library,  
trying to understand what was happening.  
I believe I've found the cause and have a fix, but I still struggle to reason about what's happening under the hood so I might be wrong.  
  
I believe the issue lies in `boost::fibers::asio::round_robin`'s `notify()` method. The method simply  
make the timer expires `now()`. In the past I guess this was okay, but since commit de58b076ebb19f881f3aa8401a9689b6ef481680 the lambda posted in the `round_robin::service`'s contructor does not `yield()` anymore. This means that when that lambda is awakened due to the timer expiring, it doesn't give a chance to other fiber to run.  
  
The fix consist of adding an `async_wait()` that would run `yield()`.  
I'll submit a PR with the fix shortly. Note that some of the comment are probably outdated, but I'm not  
comfortable enough to update them.   
  
Please let me know if my understanding of the issue is wrong.

---

## Comment 1

> Username: olk  
> Created at: 2016-11-27 10:43:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/100#issuecomment-263114931  

thx, added
