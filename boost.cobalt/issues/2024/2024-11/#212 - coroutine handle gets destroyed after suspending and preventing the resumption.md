# #212 - coroutine handle gets destroyed after suspending and preventing the resumption [Closed]

> Username: mitsubishirgb  
> Created at: 2024-11-08 22:27:50 UTC  
> Updated at: 2024-11-11 15:54:39 UTC  
> Closed at: 2024-11-11 15:54:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/212  

```cpp  
template<typename T>  
struct Future   
{  
    auto operator co_await() { return *this; };  
    bool await_ready() const noexcept {  
        return is_ready_;  
    }  
  
      
    void await_suspend(std::coroutine_handle<> h) noexcept {  
        handle_ = h;   
        if (handle_) {  
            std::cout << "Handle " << handle_.address() << std::endl;  
        }  
        std::cout << "Coroutine handle set in await_suspend.\n";  
    }  
  
  
    T await_resume() {  
        if (!value_) throw std::runtime_error("No value set in Future");  
        return std::move(*value_);  
    }  
  
  
  
    void set_value(T value) {  
        value_ = std::move(value);  
        is_ready_ = true;  
  
        if (handle_) {  
            std::cout << "Resuming coroutine.\n";  
            handle_.resume();  
        }  
        else {  
            std::cerr << "Coroutine handle not initialized.\n";  
        }  
    }  
  
    T get_value() const {  
        if (!is_ready_) throw std::runtime_error("Future value not set");  
        return *value_;  
    }  
  
private:  
    std::coroutine_handle<void> handle_ = std::coroutine_handle<void>::from_address(nullptr);  
    std::optional<T> value_;  
    bool is_ready_{ false };  
};  
  
std::queue<Future<int>> futures;  
  
cobalt::promise<void> coro() {  
    Future<int> future;  
    futures.push(std::move(future));  
  
    std::cout << "Suspending coroutine" << std::endl;  
    co_await future;   
    std::cout << "Resuming coroutine" << std::endl;  
    co_return;  
}  
  
cobalt::main co_main(int argc, char** argv) {  
  
    auto coro1 = coro();  
    auto& future = futures.front();  
    future.set_value(5);  
  
    std::cout << "Finished successfully. Future value: " << future.get_value() << std::endl;  
  
    co_return 0;  
}  
```  
  
This is the output as you can see the "resume corutine" is not printed:  
Suspending coroutine  
Handle 000001771093D208  
Coroutine handle set in await_suspend.  
Coroutine handle not initialized.  
Finished successfully. Future value: 5

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-11-08 23:52:29 UTC  
> Url: https://github.com/boostorg/cobalt/issues/212#issuecomment-2465912545  

You're awaiting a moved-from `future` with `co_await future;`. I.e. you are calling `set_value()` on a different future than the one you're awaiting in the coro.  
  
I would suggest you use `cobalt::unique_handle` instead of `coroutine_handle` in your future, too.

---

## Comment 2

> Username: mitsubishirgb  
> Created at: 2024-11-11 01:36:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/212#issuecomment-2467066064  

> You're awaiting a moved-from `future` with `co_await future;`. I.e. you are calling `set_value()` on a different future than the one you're awaiting in the coro.  
>   
> I would suggest you use `cobalt::unique_handle` instead of `coroutine_handle` in your future, too.  
  
i set the queue to hold Future pointers expecting fixation but the problem persisted, then i also removed the overloaded co_await and the handler was edible for resume which i find it very odd
