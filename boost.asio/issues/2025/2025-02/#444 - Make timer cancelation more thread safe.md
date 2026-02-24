# #444 - Make timer cancelation more thread safe [Closed]

> Username: kuznetsss  
> Created at: 2025-02-25 16:40:46 UTC  
> Updated at: 2025-02-26 14:20:43 UTC  
> Closed at: 2025-02-26 14:05:14 UTC  
> Url: https://github.com/boostorg/asio/issues/444  

Hi,  
  
Would it be safe to swap these lines in timer implementation or will it break something?  
https://github.com/boostorg/asio/blob/032545fc569553ff9a1f142dd9cec1a9e070a4f9/include/boost/asio/detail/deadline_timer_service.hpp#L166-L167  
  
I have an example where the current code is not thread safe, but if lines are swapped it probably will be:  
```cpp  
  
class WorkerPool {  
    boost::asio::thread_pool ctx_{12};  
  
public:  
    void  
    doWork(std::function<void()> onWorkComplete)  
    {  
        boost::asio::spawn(ctx_, [onWorkComplete = std::move(onWorkComplete)](boost::asio::yield_context yield) {  
            // sleep 20ms to emulate work  
            boost::asio::steady_timer timer{yield.get_executor(), std::chrono::milliseconds{20}};  
            timer.async_wait(yield);  
            onWorkComplete();  
        });  
    }  
};  
  
int main() {  
      WorkerPool workerPool;  
      boost::asio::thread_pool ctx{12};  
  
      for (int i = 0; i < 6; ++i) {  
          boost::asio::spawn(ctx, [&](boost::asio::yield_context) {  
              while (true) {  
                  boost::asio::spawn(ctx, [&workerPool](boost::asio::yield_context yield) {  
                      boost::asio::steady_timer workDoneWaiter{yield.get_executor(), boost::asio::steady_timer::duration::max()};  
                      auto onWorkComplete = [&workDoneWaiter]() { workDoneWaiter.cancel(); };  
  
                      workerPool.doWork(onWorkComplete);  
  
                      boost::system::error_code error;  
                      workDoneWaiter.async_wait(yield[error]); // wait for onWorkComplete() to be called  
                  });  
                  std::this_thread::sleep_for(std::chrono::milliseconds{1}); // spawn 1000 tasks per second  
              }  
          });  
      }  
      ctx.join();  
  }  
```  
  
Here when `onWorkComplete()` is called, it calls `timer.cancel()` which internally calls [`boost::asio::detail::deadline_timer_service::cancel()`](https://github.com/boostorg/asio/blob/master/include/boost/asio/detail/deadline_timer_service.hpp#L155).  
  
Inside `deadline_timer_service::cancel()` there are lines I mentioned above:  
```cpp  
std::size_t count = scheduler_.cancel_timer(timer_queue_, impl.timer_data);  
impl.might_have_pending_waits = false;  
```  
  
In my example there is a non-zero probability that after `scheduler_.cancel_timer()` is called another thread will delete the timer and consequently `impl` will be deleted. So touching `impl.might_have_pending_waits` will be use-after-free.  To fix the example currently I have to put `workDoneWaiter` into a `std::shared_ptr`.  
  
But if it is possible to swap the lines in `deadline_timer_service`, `impl` wouldn't be touched after `scheduler_.cancel_timer()` and my example would work without any modifications.  
  
cc: @chriskohlhoff

---

## Comment 1

> Username: ashtum  
> Created at: 2025-02-25 17:32:43 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2682774694  

`might_have_pending_waits` is not an atomic variable, and modifying the line will not guarantee the correct ordering.  
  
I didn't understand what you are trying to achieve. Is there any reason why you don't do something like this:  
  
```C++  
    auto onWorkComplete = [&workDoneWaiter]()  
    {  
        asio::post(  
            workDoneWaiter.get_executor(),  
            [&workDoneWaiter]() { workDoneWaiter.cancel(); })  
    };  
```

---

## Comment 2

> Username: kuznetsss  
> Created at: 2025-02-25 17:45:38 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2682834098  

Wow, this fixes my example, thanks @ashtum!  
Could you please explain what is the difference in this case between the direct cancel and posting cancel?

---

## Comment 3

> Username: ashtum  
> Created at: 2025-02-25 18:00:22 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2682869981  

> Could you please explain what is the difference in this case between the direct cancel and posting cancel?  
  
`asio::post` submits a function object for execution on the provided executor, ensuring that `workDoneWaiter.cancel();` runs on the correct executor.  
  
Although in this line, you are passing a multi-threaded executor to the timer:  
```C++  
boost::asio::steady_timer workDoneWaiter{yield.get_executor(), boost::asio::steady_timer::duration::max()};  
```  
  
You need to wrap the executor in this line in an strand to guarantee serial execution:  
```C++  
      for (int i = 0; i < 6; ++i) {  
          boost::asio::spawn([ctx](asio::make_strand(ctx)), [&](boost::asio::yield_context) {  
```

---

## Comment 4

> Username: kuznetsss  
> Created at: 2025-02-25 18:12:23 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2682898429  

Coroutines already have a strand inside. Probably I could do something like:  
```cpp  
auto onWorkComplete = [&workDoneWaiter, yield]()  
    {  
        asio::spawn(  
           yield,  
            [&workDoneWaiter](auto&&) { workDoneWaiter.cancel(); });  
    };  
```  
To reuse the same coroutine. Right?

---

## Comment 5

> Username: ashtum  
> Created at: 2025-02-25 18:22:46 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2682920693  

> Coroutines already have a strand inside. Probably I could do something like:  
  
Did you read that in the documentation?    
Asio examples use a strand for spawning a stackful coroutine:  
https://github.com/boostorg/asio/blob/032545fc569553ff9a1f142dd9cec1a9e070a4f9/example/cpp11/spawn/echo_server.cpp#L55  
  
If you spawn the stackful coroutine via a strand, its executor will be inherited in the following line:  
```C++  
boost::asio::steady_timer workDoneWaiter{yield.get_executor(), boost::asio::steady_timer::duration::max()};  
```

---

## Comment 6

> Username: kuznetsss  
> Created at: 2025-02-25 20:25:24 UTC  
> Updated at: 2025-02-25 21:13:58 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2683205368  

Hm, I couldn't find it in the documentation, but a few StackOverflow questions say that:  
  
> Stackful coroutines use a strand to help guarantee the coroutine yields before resume is invoked.  
  
https://stackoverflow.com/a/26728121/27236555  
https://stackoverflow.com/a/30560228/27236555  
  
In the example you are reffering to multiple coroutines are spawned on the same strand which is a different case. I'm saying that each coroutine has an internal strand and operations inside one coroutine are executed on it.  
  
Update:  
It is easy to check whether operations in one coroutine are sequential.  
```cpp  
boost::asio::thread_pool ctx{10};  
  
    auto start = std::chrono::steady_clock::now();  
  
    boost::asio::spawn(ctx, [](boost::asio::yield_context yield) {  
        for (int i = 0; i < 5; ++i) {  
            boost::asio::spawn(yield, [i](boost::asio::yield_context ) {  
                std::this_thread::sleep_for(std::chrono::milliseconds(500));  
                std::cout << i << "\n";  
            });  
        }  
    });  
  
    std::cout << "waiting\n";  
    ctx.join();  
    std::cout << "done\n";  
  
    auto end = std::chrono::steady_clock::now();  
    std::cout << "Elapsed time in milliseconds : "  
              << std::chrono::duration_cast<std::chrono::milliseconds>(end - start).count() << " ms" << std::endl;  
```  
This code prints:  
```  
waiting  
0  
1  
2  
3  
4  
done  
Elapsed time in milliseconds : 2520 ms  
```  
Which means that all sub coroutines where executed sequentially. If execution would be in parallel here, elapsed time would be  around 500 ms because there are 10 threads and 5 sleeps.

---

## Comment 7

> Username: ashtum  
> Created at: 2025-02-26 11:25:59 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2684687340  

> Which means that all sub coroutines where executed sequentially. If execution would be in parallel here, elapsed time would be around 500 ms because there are 10 threads and 5 sleeps.  
  
I believe this doesn't mean it uses an internal strand. It simply shows that the `yield_context` suspends the stackful coroutine before initiating the async operation, ensuring that resumption happens afterward.  
  
If your code guarantees that the timer object is never accessed from multiple threads, you don't need to use a strand.  
However, in this part of the code:  
  
```C++  
                      workerPool.doWork(onWorkComplete);  
  
                      boost::system::error_code error;  
                      workDoneWaiter.async_wait(yield[error]); // wait for onWorkComplete() to be called  
```  
  
`workDoneWaiter.cancel()` could be called in parallel with `workDoneWaiter.async_wait()`.

---

## Comment 8

> Username: kuznetsss  
> Created at: 2025-02-26 12:11:37 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2684784122  

> I believe this doesn't mean it uses an internal strand. It simply shows that the yield_context suspends the stackful coroutine before initiating the async operation, ensuring that resumption happens afterward.  
  
Doesn't this mean that it is not possible to run multiple async parts of one coroutine or multiple coroutines spawned on current yield_context in parallel?   
  
I can see that there is an overload of `spawn()` adding strand in the version 1.82 (which I'm using): https://github.com/boostorg/asio/blob/boost-1.82.0/include/boost/asio/impl/spawn.hpp#L1572  
But I couldn't be sure exactly this overload is called. I believe the behaviour where parts of a coroutine or sub coroutines could not be executed in parallel was not changed in a newer versions.  
  
So changing this part fixes the problem in my example:  
```cpp  
    auto onWorkComplete = [&workDoneWaiter, yield]()  
    {  
        asio::spawn(  
           yield,  
           [&workDoneWaiter](auto&&) { workDoneWaiter.cancel(); }  
        );  
    };  
```  
Because now `workDoneWaiter` will be touched only from one thread at a time. Is it correct?

---

## Comment 9

> Username: ashtum  
> Created at: 2025-02-26 12:24:20 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2684811765  

> I can see that there is an overload of `spawn()` adding strand in the version 1.82 (which I'm using): https://github.com/boostorg/asio/blob/boost-1.82.0/include/boost/asio/impl/spawn.hpp#L1572 But I couldn't be sure exactly this overload is called. I believe the behaviour where parts of a coroutine or sub coroutines could not be executed in parallel was not changed in a newer versions.  
  
This has changed recently: https://github.com/boostorg/asio/commit/df973a85ed69f02162dc091d368ab1efb86caee9  
  
> So changing this part fixes the problem in my example:  
>   
>     auto onWorkComplete = [&workDoneWaiter, yield]()  
>     {  
>         asio::spawn(  
>            yield,  
>            [&workDoneWaiter](auto&&) { workDoneWaiter.cancel(); }  
>         );  
>     };  
> Because now `workDoneWaiter` will be touched only from one thread at a time. Is it correct?  
  
No, because you are accessing the timer while the original coroutine might be just about to call `async_wait` on it.

---

## Comment 10

> Username: kuznetsss  
> Created at: 2025-02-26 13:11:06 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2684918421  

> This has changed recently: https://github.com/boostorg/asio/commit/df973a85ed69f02162dc091d368ab1efb86caee9  
  
Does it mean that before this commit coroutines worked as I expect, but since boost 1.87 users should add an explicit strand?  
  
> No, because you are accessing the timer while the original coroutine might be just about to call async_wait on it.  
  
Which version of boost are you talking about?  
  
Sorry that I'm asking so many questions, I'm trying to understand coroutines guarantees Asio provides.

---

## Comment 11

> Username: ashtum  
> Created at: 2025-02-26 13:57:58 UTC  
> Updated at: 2025-02-26 14:20:43 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2685106872  

> > This has changed recently: [df973a8](https://github.com/boostorg/asio/commit/df973a85ed69f02162dc091d368ab1efb86caee9)  
>   
> Does it mean that before this commit coroutines worked as I expect, but since boost 1.87 users should add an explicit strand?  
  
Yes, the old version that uses explicit strand should work fine.  
  
Given that, the following code should work fine because `workDoneWaiter` is constructed using the coroutine's executor which is a strand.  
  
```  
    auto onWorkComplete = [&workDoneWaiter]()  
    {  
        asio::post(  
            workDoneWaiter.get_executor(),  
            [&workDoneWaiter]() { workDoneWaiter.cancel(); })  
    };  
```  
  
By the way, you might want to change your code to explicitly pass a strand to `spawn`. In the old version, there is an overload that skips wrapping the executor if it is already a strand. So, your code will work fine if you update Boost without any overhead.

---

## Comment 12

> Username: kuznetsss  
> Created at: 2025-02-26 14:05:14 UTC  
> Url: https://github.com/boostorg/asio/issues/444#issuecomment-2685126882  

Thank you so much for explaining this! Now it's all clear for me. Updating to boost 1.87 will be challenging though.
