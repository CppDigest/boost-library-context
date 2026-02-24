# #189 - How to use channels from a C API callback? [Closed]

> Username: jskri  
> Created at: 2024-07-15 13:01:22 UTC  
> Updated at: 2025-07-04 10:50:24 UTC  
> Closed at: 2025-07-04 10:50:24 UTC  
> Url: https://github.com/boostorg/cobalt/issues/189  

I want to use a C API in a pipeline of coroutines communicating through channels. This is my first try with coroutines and my knowledge of them is limited.  
  
The shape of the pipeline is:  
  
```  
 --------    1    ---------    2    ------  
| source | ----> | process | ----> | sink |  
 --------         ---------         ------  
```  
  
Each box represents a coroutine and each arrow a channel.  
  
The C API is used in the `process` coroutine.  
  
Its signature is roughly: `bool start_work(consumer_callback)`. This API is synchronous and calls `consumer_callback` once for each data it produces.  
  
I first considered writing to the channel 2 (see diagram above) in the callback, but this would change the signature of the callback so it's not possible.  
  
I changed to pass a coroutine handle to the callback, which resumes it. The resumed coroutine then writes the data to the channel 2.  
  
The simplified code is:  
  
```c++  
#include <coroutine>  
#include <optional>  
#include <string>  
#include <boost/cobalt/channel.hpp>  
#include <boost/cobalt/main.hpp>  
#include <boost/cobalt/promise.hpp>  
#include <boost/cobalt/join.hpp>  
  
namespace cobalt = boost::cobalt;  
  
// Data to communicate between the callback and the channel writer.  
struct Data {  
   std::optional<int> result;  
   bool done = false;  
   std::coroutine_handle<> coro_handle;  
};  
  
using Callback = void (*)(int, void*, bool);  
  
void consumer_callback(int i, void* data, bool done) {  
   Data& data_ = *reinterpret_cast<Data*>(data);  
   data_.done = done;  
   if (!done) {  
      data_.result = i;  
   }  
   data_.coro_handle.resume();  
}  
  
// C API that produces results and calls the callback to consume each result.  
// Results are integers.  
void start_work(void* data, Callback cb) {  
    bool done = false;  
    for (int i = 0; i < 10; ++i) {  
       cb(i, data, done); // !done case  
    }  
    done = true;  
    cb(0, data, done); // done case  
}  
  
struct Awaiter : std::suspend_always {  
    Data& data;  
    bool first;  
  
    bool await_ready() {  
        return data.result.has_value();  
    }  
  
    void await_suspend(std::coroutine_handle<> h) {  
        data.coro_handle = h;  
        if (first) start_work(&data, consumer_callback);  
    }  
  
    int await_resume() {  
        assert(data.result.has_value());  
        auto opt = std::exchange(data.result, std::nullopt);  
        return opt.value();  
    }  
};  
  
Awaiter nextResult(Data& data, bool first) {  
    return {{}, data, first};  
}  
  
cobalt::promise<void> source(cobalt::channel<std::string>& out) {  
    co_await out.write("Hello world!");  
    out.close();  
}  
  
cobalt::promise<void> process(cobalt::channel<std::string>& in, cobalt::channel<int>& out) {  
    Data data;  
    while (in.is_open() && out.is_open()) {  
        auto _ = co_await in.read(); // ignore result for now  
        auto first = true;  
        while (!data.done || data.result.has_value()) {  
            auto i = co_await nextResult(data, first);  
            co_await out.write(i);  
            first = false;  
        }  
    }  
    in.close();  
    out.close();  
}  
  
cobalt::promise<void> sink(cobalt::channel<int>& in) {  
    while (in.is_open()) {  
        auto i = co_await in.read(); // ignore result for now  
    }  
    in.close();  
}  
  
cobalt::main co_main(int argc, char* argv[]) {  
    cobalt::channel<std::string> a;  
    cobalt::channel<int> b;  
    co_await cobalt::join(  
        source(a),  
        process(a, b),  
        sink(b)  
    );  
    co_return 0;  
}  
```  
  
The sink correctly receives all data, but when the `process` coroutine is done, there is inside Asio a coroutine resume to the null pointer. If the problem comes from resuming from inside an `await_resume`, what would be a correct design? Thanks!  
  
Environment:  
  
Ubuntu 20.04  
  
Boost 1.85  
  
g++13 -std=gnu++2a

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-07-19 01:10:07 UTC  
> Url: https://github.com/boostorg/cobalt/issues/189#issuecomment-2237834259  

I assume the callback gets invoked multiple times? If that's the case you can't just .resume the coroutine, you'd need to buffer the calls. I think you might want to use `asio::experimental::channel` or `concurrent_channel` (if multi-threaded) instead, with `async_send(..., asio::detached)`  and then do the `async_receive(cobalt::use_op)`.

---

## Comment 2

> Username: jskri  
> Created at: 2024-07-19 13:41:36 UTC  
> Updated at: 2024-07-19 14:04:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/189#issuecomment-2239211866  

Thank you for your answer. Indeed, the callback is called multiple times.  
  
Following your advice, I've come up with the following code that seems to work:  
  
```c++  
#include <boost/asio/experimental/channel.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/cobalt/gather.hpp>  
#include <boost/cobalt/op.hpp>  
#include <boost/cobalt/channel.hpp>  
#include <boost/cobalt/main.hpp>  
#include <boost/cobalt/promise.hpp>  
#include <boost/system/error_code.hpp>  
#include <string>  
  
namespace cobalt = boost::cobalt;  
namespace asio = boost::asio;  
using error_code = boost::system::error_code;  
  
using Callback = void (*)(int, void*);  
  
void consumer_callback(int i, void* data) {  
    using Chan = asio::experimental::channel<void (error_code, int)>;  
    Chan& out = *reinterpret_cast<Chan*>(data);  
    out.async_send(error_code{}, i, asio::detached);  
}  
  
// C API that produces results and calls the callback to consume each result.  
// Results are integers.  
int start_work(void* data, Callback cb) {  
    int data_count = 10;  
    for (int i = 0; i < data_count; ++i) {  
        cb(i, data);  
    }  
    return data_count;  
}  
  
cobalt::promise<void> source(cobalt::channel<std::string>& out) {  
    co_await out.write("Hello world!");  
    out.close();  
}  
  
// NEW: use an asio::experimental::channel to communicate with the callback. See <1>, <2>.  
cobalt::promise<void> process(cobalt::channel<std::string>& in, cobalt::channel<int>& out) {  
    while (in.is_open() && out.is_open()) {  
        auto _ = co_await in.read(); // ignore result for now  
        asio::experimental::channel<void(error_code, int)> internal_chan{  
            co_await cobalt::this_coro::executor, 1u};  
        int data_count = start_work(&internal_chan, consumer_callback); // <1>  
        while (data_count > 0) {  
            int res = co_await internal_chan.async_receive(cobalt::use_op); // <2>  
            co_await out.write(res);  
            --data_count;  
        }  
    }  
    in.close();  
    out.close();  
}  
  
cobalt::promise<void> sink(cobalt::channel<int>& in) {  
    while (in.is_open()) {  
        auto _ = co_await in.read(); // ignore result for now  
    }  
}  
  
cobalt::main co_main(int argc, char* argv[]) {  
    cobalt::channel<std::string> a;  
    cobalt::channel<int> b;  
    co_await cobalt::gather(  
        source(a),  
        process(a, b),  
        sink(b)  
    );  
    co_return 0;  
}  
```  
  
However, in my real use case data produced by the C API is heavy and I want to avoid buffering. Ideally, in the pipeline data produced by a coroutine is immediately consumed by the next one.  
  
I have some code that does not buffer by using very basic custom channels:  
  
```c++  
#include <coroutine>  
#include <optional>  
#include <string>  
#include <utility>  
#include <boost/system/error_code.hpp>  
  
struct Read {};  
inline Read read() { return {}; }  
  
// A simple channel that can hold one value.  
template<typename T>  
struct channel {  
    struct promise_type;  
private:  
    std::coroutine_handle<promise_type> h;  
public:  
    explicit channel(std::coroutine_handle<promise_type> h) : h(h) {}  
    auto write(T t)    { h.promise().last = t; if (!h.done()) h.resume(); }  
    auto close()       { if (!h.done()) h.resume(); }  
};  
  
template<typename T>  
struct channel<T>::promise_type {  
    using coro_handle = std::coroutine_handle<promise_type>;  
    std::optional<T> last;  
    auto get_return_object()      { return channel{coro_handle::from_promise(*this)}; }  
    auto initial_suspend()        { return std::suspend_never{}; }  
    auto final_suspend() noexcept { return std::suspend_always{}; }  
    auto unhandled_exception()    { if (auto p = std::current_exception(); p) std::rethrow_exception(p); }  
    auto return_void()            {}  
    struct ReadAwaiter {  
        std::optional<T>& last;  
        bool await_ready()                { return last.has_value(); }  
        auto await_suspend(coro_handle)   {}  
        auto await_resume() -> std::optional<T> {  
            return std::exchange(last, std::nullopt); // Could be none because of resume on channel close.  
        }  
    };  
    auto await_transform(Read)    { return ReadAwaiter{last}; }  
};  
  
using error_code = boost::system::error_code;  
  
using Callback = void (*)(int, void*);  
  
void consumer_callback(int i, void* data) {  
    channel<int>& out = *reinterpret_cast<channel<int>*>(data);  
    out.write(i);  
}  
  
// C API that produces results and calls the callback to consume each result.  
// Results are integers.  
void start_work(void* data, Callback cb) {  
    for (int i = 0; i < 10; ++i) {  
        cb(i, data);  
    }  
}  
  
channel<std::string> process(channel<int>& out) {  
  while (true) {  
    auto opt_text = co_await read();  
    if (!opt_text.has_value()) break;  
    start_work(&out, consumer_callback); // gnore opt_text for now  
  }  
}  
  
channel<int> sink() {  
  while (true) {  
    auto opt_res = co_await read();  
    if (!opt_res.has_value()) break;  
    auto i = opt_res.value();  
  }  
}  
  
int main(int argc, char* argv[]) {  
  auto b = sink();  
  auto a = process(b);  
  a.write("Hello world!");  
  a.write("I'm fine thank you.");  
  a.close();  
  return 0;  
}  
```  
  
I was wondering if it was possible to do something similar with Cobalt and channels (i.e. avoid buffering).

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2024-07-26 01:32:22 UTC  
> Url: https://github.com/boostorg/cobalt/issues/189#issuecomment-2251787629  

You can write your own awaitable - that's actually encouraged. But I don't know how you would handle the callback being invoked multiple times. This seems like trouble waiting to happen: what happens if the callback gets invoked without someone waiting?  
  
Below is a snippet to get you started, which assumes a *single invocation* from the *same thread*.  
  
```cpp  
//   
struct my_awaitable   
{  
   int res;  
   std::coroutine_handle<void> me;   
     
   static void consumer_callback(int i, void* data) {  
      auto & aw = *static_cast<my_awaitable*>(data);  
      aw.res = i; // assign the result;  
      me(); // resume  
   }  
     
   bool await_ready() {return false;] // always suspend!  
   void await_suspend(std::coroutine_handle<void> h) // called after coro is suspended  
   {  
       start_work(this, &consumer_callback);  
   }  
   int await_resume() {return res;}  
};  
  
// and then just  
  
int r = co_await my_awaitable{};  
```  
  
If you want to have N invocations you need to somehow map them to M co_await statements. Additionally,  you'll need to consider what happens when the callback doesn't get invoked. In the above code, we'd have a memory leak if the callback doesn't get invoked.
