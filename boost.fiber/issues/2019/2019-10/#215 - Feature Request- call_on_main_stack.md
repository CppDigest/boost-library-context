# #215 - Feature Request: call_on_main_stack [Closed]

> Username: rockeet  
> Created at: 2019-10-25 05:55:24 UTC  
> Updated at: 2022-09-18 14:43:06 UTC  
> Closed at: 2022-09-18 14:43:06 UTC  
> Url: https://github.com/boostorg/fiber/issues/215  

## Background  
Sometimes we need to call a function which needs large stack in a fiber, but a fiber just has a small stack, which may cause stack overflow. If we know this large stack function will not cause fiber switch, then we can call the large function on the `main fiber` which has a relative large stack.  
  
Another issue is, when calling functions of other languages(such as call Java in JNI) from a `user fiber`, such VM(such as JVM) may check stack and refuse `user fiber`'s stack. We also need to call such functions on `main fiber`'s stack when we are in a `user fiber`.  
  
## My Imperfect Solution  
I tried to hack a `boost::fibers::scheduler::call_on_main_stack()` by myself, as below:  
```c++  
void  
scheduler::call_on_main_stack(const std::function<void()>& largeStackFn)  
noexcept {  
    std::move(main_ctx_->c_).resume_with([&](boost::context::fiber&& prev_c) {  
        largeStackFn();  
        std::move(prev_c).resume_with([this](boost::context::fiber&& main_c) {  
            main_ctx_->c_ = std::move(main_c);  
            return boost::context::fiber{};  
        });  
        return boost::context::fiber{};  
    });  
}  
```  
## Issues of My Solution  
Then I realized that infinitely calling my `call_on_main_stack` may overflow main stack, because each call to this function will put some useless data on the main stack which will be used when `main fiber` get resumed.  
  
## Conclusion  
We may need an essential solution for this problem.

---

## Comment 1

> Username: rockeet  
> Created at: 2019-10-30 06:23:40 UTC  
> Updated at: 2019-11-01 09:54:23 UTC  
> Url: https://github.com/boostorg/fiber/issues/215#issuecomment-547755827  

1. I found that fiber is a pointer to stack, and I tried to hard backup and restore the fiber content(the pointer to stack), but it segfaults.  
  
2. Then I blindly backup some bytes near the stack top, then it works, here is my code:  
```c++  
void  
scheduler::call_on_main_stack(const std::function<void()>& largeStackFn)  
noexcept {  
    assert(!context::active()->c_);  
    if (BOOST_LIKELY((bool)main_ctx_->c_)) {  
        assert(context::active() != main_ctx_);  
        static constexpr size_t backup_size = 64; // 64 for Linux & Mac X86_64  
        unsigned char old_main_stack_top[backup_size];  
        // context::fiber is just a point to fiber stack  
        void* old_main_sp = (void*&)(main_ctx_->c_);  
        memcpy(old_main_stack_top, old_main_sp, backup_size);  
        std::move(main_ctx_->c_).resume_with([&](boost::context::fiber&& prev_c) {  
            largeStackFn();  
            std::move(prev_c).resume_with([this](boost::context::fiber&& main_c) {  
                main_ctx_->c_ = std::move(main_c);  
                return boost::context::fiber{};  
            });  
            return boost::context::fiber{};  
        });  
        memcpy(old_main_sp, old_main_stack_top, backup_size);  
        (void*&)(main_ctx_->c_) = old_main_sp;  
    }  
    else {  
        assert(context::active() == main_ctx_);  
        largeStackFn();  
    }  
}  
```  
  
3. But I still want a preferable official solution from you the author of fiber.
