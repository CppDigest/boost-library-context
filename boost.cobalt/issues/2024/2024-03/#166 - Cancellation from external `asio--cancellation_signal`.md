# #166 - Cancellation from external `asio::cancellation_signal`? [Closed]

> Username: Lyulf  
> Created at: 2024-03-25 18:07:18 UTC  
> Updated at: 2024-04-01 18:13:42 UTC  
> Closed at: 2024-04-01 18:13:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166  

I have coroutines that need to be cancelled in order for me to correctly handle cleanup of a class.  
I have tried using `reset_cancellation_source` directly on `promise_type` (since `cobalt::this_coro::reset_cancellation_source` doesn't work outside `cobalt::detached`) but cancellation signal is ignored if sent before cancellation source is reset and cancellation stops working when called directly on the coroutine (e.g. from `cobalt::race` ).  
  
I can probably work around those problems but I was wondering if there is a built in way to do this? If not then are there any plans for adding that in the future?  
  
Here is an example code to hopefully demonstrate what I want to achieve (sorry for the length):  
```c++  
#include <coroutine>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
// Required because cobalt::this_coro::reset_cancellation_source is available only for  
// cobalt::detached  
struct ResetCancellationSource  
{  
  asio::cancellation_slot slot;  
  
  bool await_ready() { return false; }  
  template <typename Promise>  
  bool await_suspend(std::coroutine_handle<Promise> handle)  
  {  
    handle.promise().reset_cancellation_source(std::move(slot));  
    return false;  
  }  
  void await_resume() {}  
};  
  
// Assume that implementations can change value of locked and assume that lockImpl can suspend and  
// resume multiple times until lock is ready  
cobalt::task<void> lockImpl(bool& locked);  
cobalt::task<void> unlockImpl(bool& locked);  
  
cobalt::task<void> cleanUp(bool& locked)  
{  
  // Without cancellation value could potentially be changed after cleanup  
  if (locked)  
    co_await unlockImpl(locked);  
}  
  
class Lock  
{  
public:  
  Lock(cobalt::executor executor = cobalt::this_thread::get_executor()) : executor(executor) {}  
  
  ~Lock()  
  {  
    signal.emit(asio::cancellation_type::all);  
    cobalt::spawn(executor, cleanUp(locked), asio::use_future).get();  
  }  
  
  cobalt::promise<void> lock()  
  {  
    // Is there a way to do this in a way that still allows caller to cancel lock and doesn't ignore  
    // cancellation if signal was triggered before reseting the source?  
    co_await ResetCancellationSource{signal.slot()};  
  
    // example impl  
    co_await cobalt::spawn(executor, lockImpl(locked), cobalt::use_op);  
  }  
  
  cobalt::promise<void> unlock()  
  {  
    // same as above  
    co_await ResetCancellationSource{signal.slot()};  
  
    co_await cobalt::spawn(executor, unlockImpl(locked), cobalt::use_op);  
  }  
  
private:  
  cobalt::executor executor;  
  asio::cancellation_signal signal;  
  bool locked = false;  
};  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-03-26 01:06:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2019204721  

I don't understand the problem. Why don't you just spawn a task and use `asio::bind_cancellation_slot` on the completion token?

---

## Comment 2

> Username: Lyulf  
> Created at: 2024-03-28 04:03:28 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2024355900  

This is exactly what I was looking for. I knew that it should be simple to achieve but I guess my lack of experience with asio must have been showing.

---

## Comment 3

> Username: Lyulf  
> Created at: 2024-03-28 09:47:37 UTC  
> Updated at: 2024-03-28 12:16:35 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2024792741  

I guess I was too hasty will closing the issue. After more testing I found that using `asio::bind_cancellation_slot` seems to still prevent cancellation which is called directly on a promise.  
  
Here is an example:  
```c++  
#include <cassert>  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
using namespace std::chrono_literals;  
  
struct SuspendUntilCancelled  
{  
  struct Cancellation  
  {  
    cobalt::executor executor;  
    void* address = nullptr;  
  
    void operator()(asio::cancellation_type /*type*/)  
    {  
      if (address != nullptr)  
        asio::post(executor, cobalt::unique_handle<void>(address));  
    }  
  };  
  
  bool await_ready() { return false; }  
  
  template <typename Promise>  
  void await_suspend(std::coroutine_handle<Promise> handle)  
  {  
    auto executor = handle.promise().get_executor();  
    slot = handle.promise().get_cancellation_slot();  
    assert(slot.is_connected());  
    slot.emplace<Cancellation>(executor, handle.address());  
  }  
  
  void await_resume()  
  {  
    if (slot.is_connected())  
      slot.clear();  
  }  
  
  asio::cancellation_slot slot;  
};  
  
struct CancellableContainer  
{  
  cobalt::promise<void> suspendUntilCancel()  
  {  
    co_await cobalt::spawn(co_await cobalt::this_coro::executor,  
                           suspend(),  
                           asio::bind_cancellation_slot(  
                             signal.slot(), cobalt::use_op)); // using just cobalt::use_op works just fine  
  }  
  
  void cancel() { signal.emit(asio::cancellation_type::all); }  
  
  
private:  
  cobalt::task<void> suspend() { co_await SuspendUntilCancelled{}; }  
  
  asio::cancellation_signal signal;  
};  
  
cobalt::main co_main(int /*argc*/, char** /*argv*/)  
{  
  CancellableContainer container;  
  auto promise = container.suspendUntilCancel();  
  //container.cancel(); // works correctly  
  promise.cancel();  
  co_await promise; // permanently suspended if cancelled using promise.cancel()  
  co_return 0;  
}  
```

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-03-28 13:39:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2025215601  

In the above code you're intercepting the `cancel` on the promise. That is the `co_await spawn` doesn't get the cancel of the promise forwarded.

---

## Comment 5

> Username: Lyulf  
> Created at: 2024-03-28 14:55:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2025427873  

Then is there a way to allow cancellation from both `cobalt::promise` and `asio::cancellation_signal` or should I write something on my own?

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-04-01 13:25:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2029756513  

You'd need to write it on your own. You can get the cancellation_state as describe [here](https://www.boost.org/doc/libs/master/libs/cobalt/doc/html/index.html#promise_cancellation_base).

---

## Comment 7

> Username: Lyulf  
> Created at: 2024-04-01 18:13:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/166#issuecomment-2030279299  

Ok. Thank you for your help!
