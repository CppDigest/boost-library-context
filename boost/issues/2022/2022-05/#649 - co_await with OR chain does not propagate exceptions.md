# #649 - co_await with OR chain does not propagate exceptions [Open]

> Username: goodysun  
> Created at: 2022-05-23 06:25:54 UTC  
> Updated at: 2022-05-23 07:14:50 UTC  
> Url: https://github.com/boostorg/boost/issues/649  

Not sure if my expectation is correct.  
  
Expectation  
- The exception from fn3 should be propagated out to the try-catch in `someFunction`  
  
Current result  
- In the below code-snippet, the exception from fn3 is not being propagated to the try-catch  
- fn3 ends at the throw exception  
- fn1 and fn2 continue to run forever, preventing the co_await from ending  
  
  
```  
asio::awaitable<void> fn1() {  
    asio::steady_timer timer{co_await asio::this_coro::executor};  
    while (true) {  
        timer.expires_after(std::chrono::seconds(1));  
        co_await timer.async_wait(asio::use_awaitable);  
        LOG_INFO("Fn1 here");  
    }  
    co_return;  
}  
  
asio::awaitable<void> fn2() {  
    asio::steady_timer timer{co_await asio::this_coro::executor};  
    while (true) {  
        timer.expires_after(std::chrono::seconds(1));  
        co_await timer.async_wait(asio::use_awaitable);  
        LOG_INFO("Fn2 here");  
    }  
    co_return;  
}  
  
asio::awaitable<void> fn3() {  
    asio::steady_timer timer{co_await asio::this_coro::executor};  
    timer.expires_after(std::chrono::seconds(1));  
    co_await timer.async_wait(asio::use_awaitable);  
    LOG_INFO("Fn3 here");  
    throw std::runtime_error("Error from fn3!"); // fn3() ends here  
    co_return;  
}  
  
asio::awaitable<void> someFunction() {  
  
    try {  
        co_await (fn1() || fn2() || fn3());  
    } catch (std::exception &e) {  
        // For some reason, the throw from fn3 does not propagate to here  
        // fn1 and fn2 will continue to run forever  
        LOG_INFO("Exception is {}", e.what());  
    }  
    co_return;  
}  
  
  
int main(void) {  
    auto io_context = asio::io_context();  
  
    co_spawn(  
            io_context,  
            someFunction(),  
            asio::detached);  
  
    io_context.run();  
}  
```  
  
Should the right implementation of this be to break out of the co_await and propagate the exception out once one of the function throws an exception?
