# #20 - Easy to use support for Boost.Coroutine2 suspend-resumption [Closed]

> Username: ned14  
> Created at: 2023-07-14 11:04:40 UTC  
> Updated at: 2024-05-01 12:07:21 UTC  
> Closed at: 2024-05-01 12:07:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20  

Down the road, after this review, it would be great if Async gained integration with Boost.Coroutine2 such that:  
  
1. C++ coroutine with Async calls C function using a Coroutine2 stack.  
2. C function calls a C callback.  
3. C callback suspends execution of the Async C++ coroutine.  
4. When Async C++ coroutine resumes, Coroutine2 stack is restored, and the C callback returns.  
  
Then you get easy to use integration between Async stackless code and stackful coroutine code, and everybody gets to party!

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-21 17:06:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20#issuecomment-1646003091  

I would actually go down to the boost.context level. It could work like this:  
  
```cpp  
async::fiber f() // context::fiber  
{  
  // use it as a C++20 coro  would need some trickkery to work  
     
   // raw boost.context thingy - we probably need something like asio::yield_context  
   auto context::fiber && f = co_await this_coro::context;  
        
}  
```  
  
But I have to be honest, that I honestly don't know what boost.coroutine2 does for me. I've just used boost.context directly.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-03-09 08:51:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20#issuecomment-1986798066  

I couldn't resist looking further into this, and I think that'll actually work, although it would be techncially UB.   
  
On clang & gcc, this is the frame used by the noop-coro.  
  
```cpp  
    struct __noop_coroutine_frame_ty_ {  
        static void __dummy_resume_destroy_func() { }  
  
        void (*__resume_)() = __dummy_resume_destroy_func;  
        void (*__destroy_)() = __dummy_resume_destroy_func;  
        struct noop_coroutine_promise __promise_;  
    };  
```  
  
The `__resume_` and `__destroy_` functions each take a ptr to the frame as first argument (ignored here bc uneeded).  
  
Starting from Slide 140 this gets interesting.  
  
https://luncliff.github.io/coroutine/ppt/[Eng]ExploringTheCppCoroutine.pdf

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-03-09 09:10:43 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20#issuecomment-1986802341  

Not great & clang complains, but it *basically* works. It's also UB according to the C++ standard.  
  
```cpp  
  
#include <coroutine>  
#include <boost/context/fiber.hpp>  
#include <thread>  
  
struct delay  
{  
  bool await_ready() {return false;}  
  
  void await_suspend(std::coroutine_handle<> h)  
  {  
    std::thread{  
        [h]{  
          std::this_thread::sleep_for(std::chrono::milliseconds(100));  
          h.resume();  
        }}.detach();  
  }  
  
  
  void await_resume() {}  
};  
  
template<typename Awaitable>  
auto await(boost::context::fiber & outer, Awaitable && aw)  
{  
  if (!aw.await_ready())  
  {  
    struct frame  
    {  
      void(*resume)(frame *) =  
          [](frame * this_)  
          {  
             this_->fb = std::move(this_->fb).resume();  
          };  
  
      void(*destroy)(frame *) = [](frame * this_) { this_->fb = {}; };  
  
      boost::context::fiber fb;  
    };  
  
    frame f;  
  
    outer = std::move(outer).resume_with(  
        [&](boost::context::fiber && inner)  
        {  
          f.fb = std::move(inner);  
          auto h = std::coroutine_handle<>::from_address(&f);  
          printf("Doing await_suspend\n");  
          aw.await_suspend(h);  
          return boost::context::fiber{};  
        });  
  
  }  
  
  return aw.await_resume();  
}  
  
  
boost::context::fiber do_the_thing(boost::context::fiber && outer)  
{  
  printf("Starting\n");  
  await(outer, delay());  
  printf("Awaited\n");  
  
  return outer;  
}  
  
  
int main(int argc, char ** argv)  
{  
  boost::context::fiber fb{&do_the_thing};  
  
  fb = std::move(fb).resume();  
  std::this_thread::sleep_for(std::chrono::milliseconds(200));  
  printf("Exiting main\n");  
  
  return 0;  
}  
  
```

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-03-09 09:11:27 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20#issuecomment-1986802552  

Side note: setting `__resume_` to zero is how gcc & clang indicate that the coro is done.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-05-01 12:07:21 UTC  
> Url: https://github.com/boostorg/cobalt/issues/20#issuecomment-2088375394  

not coroutine2, but close enough with boost.context support in experimental. has been merged.
