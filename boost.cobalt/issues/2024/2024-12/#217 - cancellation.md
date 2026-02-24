# #217 - cancellation [Open]

> Username: mitsubishirgb  
> Created at: 2024-12-25 14:58:36 UTC  
> Updated at: 2026-02-15 15:51:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217  

Can someone just give me instructions how to cancel awaitables and what responsabilities does the caller have, i looked up the doc but it was lacking   
  
```cpp  
struct CustomCancel {  
    asio::cancellation_signal& signal_;  
  
    CustomCancel(asio::cancellation_signal& signal) : signal_(signal) {}  
    void operator()(asio::cancellation_type type = asio::cancellation_type::all) const {  
        std::cout << "cancelled " << std::endl;  
        signal_.emit(type);  
    }  
};  
  
template<typename T>  
struct Future {  
    asio::cancellation_signal cancel_sig;  
    asio::cancellation_slot cl;  
    std::exception_ptr ex;  
  
    bool await_ready() const noexcept { return done_; }  
  
    template<typename Promise>  
    void await_suspend(std::coroutine_handle<Promise> h) noexcept {  
        //Check if the promise has a cancellation slot and attach a handler if available  
        if constexpr (requires { h.promise().get_cancellation_slot(); })  
            if ((cl = h.promise().get_cancellation_slot()).is_connected()) {  
                cl.emplace<CustomCancel>(cancel_sig);  
                std::cout << "Emplaced " << std::endl;  
  
            }  
        std::cout << "handle is set" << std::endl;  
        handle_.reset(h.address());  
    }  
  
  
    auto await_resume() {  
          
        if (cl.is_connected()) {  
            cl.clear();  
              
        }  
        if (cancel_sig.slot().is_connected()) {  
            throw std::runtime_error("Operation was cancelled");  
        }  
        if (ex) {  
            std::rethrow_exception(ex);  
        }  
  
          
  
        return *value_;  
    }  
  
    template<typename Ty_>  
    void set_value(Ty_&& value) {  
  
        if (!done_ && handle_ && !handle_.done()) {  
  
            value_ = value;  
            done_ = true;  
            auto h = std::move(handle_);  
            handle_.reset();  
            h.resume();  
        }  
        else {  
            throw std::runtime_error("Either no handle or value is set");  
        }  
    }  
  
    T get_value() const {  
  
        if (done_)  
            return *value_;  
        else {  
            throw std::runtime_error("Future value not set");  
        }  
    }  
  
    bool done() const noexcept {  
        return done_;  
    }  
  
  
    void interrupt_await()& {  
        ex = cobalt::detail::detached_exception();  
        handle_.release().resume();  
    }  
private:  
    cobalt::unique_handle<void> handle_ = nullptr;  
    std::optional<T> value_;  
    bool done_{ false };  
};  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-12-26 01:15:12 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2562053852  

You will need to be more specific - what do you want to do?

---

## Comment 2

> Username: mitsubishirgb  
> Created at: 2024-12-26 03:17:12 UTC  
> Updated at: 2024-12-26 03:18:36 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2562110940  

i have an async operation on the main function that wait for signals and cancels the promises , but how do i design the future to cancel its handler too, or is it handled implicitly somehow  ?   
  
  
```  
void signal_handler(const system::error_code& ec, asio::signal_set* signals, std::vector<cobalt::promise<ClientResponse>>& tasks) {  
    if (ec) {  
        std::cerr << "Error occurred while handling signal: " << ec.message() << std::endl;  
        return;  
    }  
  
    // Signal received; cancel all tasks  
    std::cout << "SIGINT received! Cancelling all tasks..." << std::endl;  
  
    // Cancel all tasks  
    for (auto& task : tasks) {  
        task.cancel();  
    }  
  
    //signals->cancel();  
  
}  
  
asio::signal_set signal(co_await this_coro::executor, SIGINT);  
signal.async_wait([&signal, &tasks](const boost::system::error_code& ec, int signum) {  
    if (!ec) {  
        // Handle signal (SIGINT)  
        signal_handler(ec, &signal, tasks);  
    }  
    });  
  
```

---

## Comment 3

> Username: mitsubishirgb  
> Created at: 2024-12-26 03:25:42 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2562114651  

This is a snippet of my project if the coro is called more than the limit it queues the await up until a set_future() is called from other part of the code   
```  
cobalt::promise<Connection> connect(const ClientRequest& req, system::error_code& ec) {  
    ConnectionKey conn_key = req.conn_key;  
      
    if (acquired_ >= limit_) {  
        Future<std::monostate> future;  
        waiters_[conn_key].push(&future);  
        co_await future;  
    }  
  
    // IF ANY CACHED CONNECTION IN THE POOL  
    if (auto proto = get_connection(conn_key); proto != nullptr) {  
        if (proto) std::cout << "Found a connection in the pool" << std::endl;  
        co_return Connection(this, std::move(proto), &conn_key);  
  
          
    } else //std::cout << "No connection in the pool. Creating new one " << std::endl;  
  
    acquired_++;  
    ProtoPtr proto = co_await create_connection(req, ec);  
    Connection conn(this, std::move(proto), &conn_key);  
    if (ec) {  
        release_acquired();  
        release_waiters();  
        co_return Connection();  
    }  
  
    co_return conn;  
}  
```

---

## Comment 4

> Username: mitsubishirgb  
> Created at: 2024-12-27 17:34:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2563898641  

> You will need to be more specific - what do you want to do?  
  
.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2024-12-29 12:16:46 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2564706420  

I see, you're like me, you'd rather write 50 lines of code than two sentences in english explaining the intent :).  
  
So let's see:  
  
```cpp  
cobalt::promise<Connection> connect(const ClientRequest& req, system::error_code& ec) {  
    ConnectionKey conn_key = req.conn_key;  
      
    if (acquired_ >= limit_) {  
        Future<std::monostate> future;  
        waiters_[conn_key].push(&future);  
        co_await future; // <1>  
    }  
  
````  
  
The `co_await future` expression makes the `future` the awaitee, and `connect` the `awaiter` in my lingo.  
  
So you got two cases here:  
  
1. the awaiter (`connect`) gets cancelled by it's awaiter. This happens when you call `.cancel()` on a task/promise.  
  
The `co_await` will forward this to the `co_await future`. So the design question for you is: what does a `co_await future` do when the awaiter cancels? In cobalt this usually produces an `asio::error::operation_cancelled` in the form of an `error_code` or `system_error` if thrown. I.e. your implementation of `await_resume` needs to handle this. For example, `CustomCancel` could just assign an error to `ex`.  
  
However, you might want to forward the cancellation signal in some way.  
  
2. The future gets cancelled.  
  
This doesn't seems to be in the code you provided, but in this case you'll have two choices: do the same thing as above, i.e. produce an error. The awaiting coroutine can check with `co_await this_coro::cancelled` if it was cancelled or if the future was cancelled. Or you can just destroy the coroutine, so it will never get resumed. The latter is not really cancellation, but rather teardown.  
  
  
Note that the `cobalt::main co_main(int, char ***)` does handle SIGINT as cancellation for you. But cancellation only gets forwarded when you `co_await` something.

---

## Comment 6

> Username: mitsubishirgb  
> Created at: 2024-12-29 14:41:08 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2564745476  

> I see, you're like me, you'd rather write 50 lines of code than two sentences in english explaining the intent :).  
>   
> So let's see:  
>   
> cobalt::promise<Connection> connect(const ClientRequest& req, system::error_code& ec) {  
>     ConnectionKey conn_key = req.conn_key;  
>       
>     if (acquired_ >= limit_) {  
>         Future<std::monostate> future;  
>         waiters_[conn_key].push(&future);  
>         co_await future; // <1>  
>     }  
> The `co_await future` expression makes the `future` the awaitee, and `connect` the `awaiter` in my lingo.  
>   
> So you got two cases here:  
>   
> 1. the awaiter (`connect`) gets cancelled by it's awaiter. This happens when you call `.cancel()` on a task/promise.  
>   
> The `co_await` will forward this to the `co_await future`. So the design question for you is: what does a `co_await future` do when the awaiter cancels? In cobalt this usually produces an `asio::error::operation_cancelled` in the form of an `error_code` or `system_error` if thrown. I.e. your implementation of `await_resume` needs to handle this. For example, `CustomCancel` could just assign an error to `ex`.  
>   
> However, you might want to forward the cancellation signal in some way.  
>   
> 2. The future gets cancelled.  
>   
> This doesn't seems to be in the code you provided, but in this case you'll have two choices: do the same thing as above, i.e. produce an error. The awaiting coroutine can check with `co_await this_coro::cancelled` if it was cancelled or if the future was cancelled. Or you can just destroy the coroutine, so it will never get resumed. The latter is not really cancellation, but rather teardown.  
>   
> Note that the `cobalt::main co_main(int, char ***)` does handle SIGINT as cancellation for you. But cancellation only gets forwarded when you `co_await` something.  
  
Your explanation was so good it clarified the vague concepts for me , thank you for spending the time, also your observation of my lack of explanation is right, it's due to my weak english  and confusions with the topic as coming from python

---

## Comment 7

> Username: mitsubishirgb  
> Created at: 2025-03-17 23:34:07 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-2731190825  

the cancellation works fine but when we turn back to the main coroutine, co_return doesn't work properly causing an abrupt deadlock. I am guessing the main loop thinks there are unfinished tasks ? output (also i don't want to handle the cancellation with exceptions)  :  
Application started  
Coroutine cancelled  
Main coroutine is cancelled  
*deadlock  
  
source code :  
  
```  
struct CustomCancel {  
    std::coroutine_handle<> h;  
    bool* canceled;  
    void operator()(asio::cancellation_type) const {  
        *canceled = true;  
        if (h && !h.done()) h.resume();  
    }  
};  
  
template<typename T>  
struct Future {  
    // Simplified: holds a value, cancellation state, and coroutine handle  
    T* value_ = nullptr;  
    bool canceled = false;  
    std::coroutine_handle<> handle_;  
    asio::cancellation_slot cl;  
  
    auto await_ready() { return false; }  
    auto await_resume() {  
        if (canceled) return T{};  
        return *value_;  
    }  
    template<typename Promise>  
    void await_suspend(std::coroutine_handle<Promise> h) {  
        handle_ = h;  
        if constexpr (requires { h.promise().get_cancellation_slot(); }) {  
            if ((cl = h.promise().get_cancellation_slot()).is_connected()) {  
                cl.emplace<CustomCancel>(h, &canceled);  
            }  
        }  
    }  
};  
  
  
cobalt::promise<void> coro() {  
    Future<int> fut;  
    int value = co_await fut;  
  
    auto state = co_await cobalt::this_coro::cancellation_state;  
    if (state.cancelled() == asio::cancellation_type::total) {  
        std::cout << "Coroutine cancelled " << std::endl;  
        co_return;  
    }  
    std::cout << "Future finished with result: " << value << std::endl;  
    co_return;  
}  
```

---

## Comment 8

> Username: spchamp  
> Created at: 2025-07-23 01:32:52 UTC  
> Updated at: 2025-07-23 02:06:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-3105335553  

Have you perhaps taken a look at [the `cancel_after` completion adapter](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_after.html) and similar, from boost.asio?  
  
In one usage case, this completion token adapter could be applied for cancellation within a completion handler for a call to `pipe.async_read_some()` for a `pipe` as an  `asio::readable_pipe`  
  
In my own use case, it seemed that the expression `asio::cancel_after(100ms, cobalt::use_op)` needed to be wrapped in `bind_executor`. An excerpt from this usage case, illustrating the completion handler using `asio::cancel_after`  
  
```cpp  
  auto const [out_ec, n_out] = co_await cobalt::as_tuple(out_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff),  
      asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
```  
  
The complete example, using [boost.cobalt](https://www.boost.org/doc/libs/latest/libs/cobalt/doc/html/index.html) with [boost.process v2](https://www.boost.org/doc/libs/1_88_0/doc/html/process.html), boost release 1.88.0.    
  
With apologies for the verbosity in this example, the definition and usage for the objects in the excerpt above was as follows. This was adapted from an unpublished test case.  
  
```cpp  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
#include <boost/process.hpp>  
  
namespace cobalt = boost::cobalt;  
namespace asio = boost::asio;  
namespace v2 = boost::process::v2;  
namespace sys = boost::system;  
using namespace std::literals;  
  
cobalt::task<int> test_task_env_set() {  
  auto exec = co_await asio::this_coro::executor;  
   
  // create asio pipes for subprocess output and error  
  asio::writable_pipe out_wr{exec}, err_wr{exec};  
  asio::readable_pipe out_rd{exec}, err_rd{exec};  
  
  boost::asio::connect_pipe(out_rd, out_wr);  
  boost::asio::connect_pipe(err_rd, err_wr);  
  
  // create the process launcher  
  v2::default_process_launcher launcher{};  
  
  // stdio initializer for a subprocess  
  v2::process_stdio stdio_init{nullptr, out_wr, err_wr};  
  // an initializer for environment variables in a subprocess  
  // sets the environment variable "THUNK" to the value "THUNK" in the subprocess  
  v2::process_environment pe{"THUNK=THUNK"};  
  
  // using the BASH shell for purpose of brevity  
  auto const bash_cmd = v2::environment::find_executable("bash");  
  
  // bash shell script for test.  
  // fail with error output if THUNK is unset,  
  // or write the value of THUNK to stdout on success  
  std::initializer_list<std::string> args = {  
      "-c", "if [ -v \"THUNK\" ]; then echo \"THUNK=${THUNK}\"; else echo \"THUNK is "  
            "unset\" 1>&2; exit 1; fi"};  
  
  // create the subprocess  
  sys::error_code ec{};  
  auto process =  
      launcher(exec, ec, bash_cmd, args, std::move(pe), std::move(stdio_init));  
  
  BOOST_ASSERT(ec.value() == 0);  
    
  // asynchronous wait for subprocess exit  
  auto [proc_ec, exit_code] = co_await cobalt::as_tuple(process.async_wait(cobalt::use_op));  
  
  // test case follows. the cancellation is applied below  
  
  size_t constexpr buffer_size = 64;  
  char buff_data[buffer_size] = "";  
  asio::mutable_buffer m_buff = asio::buffer(buff_data);  
  
  // test stdout from the exited subprocess, with timeout  
  //  
  auto const [out_ec, n_out] = co_await cobalt::as_tuple(  
    out_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff),  
      asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
  BOOST_ASSERT(out_ec.value() == 0);  
  BOOST_ASSERT(n_out > 0);  
  
  boost::string_view s_out{buff_data, n_out - 1};  
  BOOST_ASSERT(s_out == "THUNK=THUNK");  
  
  // clear the buffer and test stederr from the exited subprocess, with timeout  
  //  
  memset(buff_data, 0, buffer_size);  
  auto const [err_ec, n_err] = co_await cobalt::as_tuple(  
    err_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff),  
      asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
  
  // when there's no error output, the read would be cancelled  
  BOOST_ASSERT(err_ec.value() == asio::error::operation_aborted);  
  BOOST_ASSERT(n_err == 0);  
  
  co_return exit_code;  
}  
  
cobalt::main co_main(int, char*[]) {  
  asio::io_context context;  
  auto f = cobalt::spawn(context, test_task_env_set(), asio::use_future);  
  context.run();  
  co_return f.get();  
}  
```  
  
  
If the linker details could be of interest, I've compiled this using cmake, linking with Boost::asio, Boost::cobalt, Boost::process, Boost::system, Boost::filesystem, and Boost::thread libraries.   
  
In this example, using BOOST_ASSERT in place of the original Catch2 REQUIRE calls, the absence of output from the test program would indicate success ....  
  
I hope that it might provide any example for using [`asio::cancel_after`](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_after.html). Of course, there's also [`cancel_at`](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_at.html)

---

## Comment 9

> Username: klemens-morgenstern  
> Created at: 2025-07-23 13:18:16 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-3108315696  

What was the issue without binding? Does  
  
```cpp  
  
  auto const [err_ec, n_err] = co_await cobalt::as_tuple(  
    err_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff), asio::cancel_after(100ms)));  
```  
  
work?

---

## Comment 10

> Username: spchamp  
> Created at: 2025-07-24 05:34:20 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-3112020081  

I wasn't able to compile this if omitting the `cobalt::use_op` expression in the completion token, or omitting the bind_executor expression, at least when the `async_read_some()` call is compiled within a `cobalt::task<int>`.    
  
I've noticed that there's a page in the [documentation](https://github.com/boostorg/cobalt/blob/57181cacc5b8d5079d95a33b7aa078d0f5f16147/doc/background/asio_awaitable.adoc#L4) denoting differences with regards to asio's `awaitable`. I wonder if perhaps could this be related?  
  
With the following:  
```cpp  
  auto const [err_ec, n_err] = co_await cobalt::as_tuple(err_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff),  
      asio::cancel_after(100ms, cobalt::use_op)));  
```  
  
I'm seeing the following from the compiler, using clang++20 with boost sources updated to the latest main:  
  
```  
[build] In file included from /home/me/wk/sandbox/project/tests/co_test_cobalt_00.cpp:2:  
[build] In file included from /usr/local/src/boost/boost_src/libs/asio/include/boost/asio.hpp:63:  
[build] In file included from /usr/local/src/boost/boost_src/libs/asio/include/boost/asio/cancel_after.hpp:301:  
[build] /usr/local/src/boost/boost_src/libs/asio/include/boost/asio/impl/cancel_after.hpp:49:32: error: no type named 'executor_type' in 'boost::asio::detail::initiation_archetype<void (boost::system::error_code, unsigned long)>'  
[build]    49 |           typename Initiation::executor_type>(this->get_executor(), timeout),  
[build]       |           ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~  
[build] /usr/local/src/boost/boost_src/libs/cobalt/include/boost/cobalt/op.hpp:244:13: note: in instantiation of function template specialization 'boost::asio::detail::initiate_cancel_after<boost::asio::detail::initiation_archetype<void (boost::system::error_code, unsigned long)>, std::chrono::steady_clock, boost::asio::wait_traits<std::chrono::steady_clock>, void (boost::system::error_code, unsigned long)>::operator()<boost::cobalt::completion_handler<boost::system::error_code, unsigned long>, long, std::ratio<1, 1000000000>>' requested here  
[build]   244 |             std::move(initiation)(std::move(complete),  
[build]       |             ^  
[build] /usr/local/src/boost/boost_src/libs/cobalt/include/boost/cobalt/op.hpp:229:10: note: in instantiation of member function 'boost::asio::async_result<boost::cobalt::use_op_t, void (boost::system::error_code, unsigned long)>::op_impl<boost::asio::detail::initiate_cancel_after<boost::asio::detail::initiation_archetype<void (boost::system::error_code, unsigned long)>, std::chrono::steady_clock, boost::asio::wait_traits<std::chrono::steady_clock>, void (boost::system::error_code, unsigned long)>, std::chrono::duration<long, std::ratio<1, 1000000000>>, boost::asio::cancellation_type>::initiate' requested here  
[build]   229 |   struct op_impl final : boost::cobalt::op<Args...>  
[build]       |          ^  
[build] 1 error generated.  
[build] ninja: build stopped: subcommand failed.  
[build]   
```

---

## Comment 11

> Username: mitsubishirgb  
> Created at: 2025-07-24 14:11:48 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-3113632622  

> Have you perhaps taken a look at [the `cancel_after` completion adapter](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_after.html) and similar, from boost.asio?  
>   
> In one usage case, this completion token adapter could be applied for cancellation within a completion handler for a call to `pipe.async_read_some()` for a `pipe` as an `asio::readable_pipe`  
>   
> In my own use case, it seemed that the expression `asio::cancel_after(100ms, cobalt::use_op)` needed to be wrapped in `bind_executor`. An excerpt from this usage case, illustrating the completion handler using `asio::cancel_after`  
>   
>   auto const [out_ec, n_out] = co_await cobalt::as_tuple(out_rd.async_read_some(  
>       std::forward<asio::mutable_buffer>(m_buff),  
>       asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
> The complete example, using [boost.cobalt](https://www.boost.org/doc/libs/latest/libs/cobalt/doc/html/index.html) with [boost.process v2](https://www.boost.org/doc/libs/1_88_0/doc/html/process.html), boost release 1.88.0.  
>   
> With apologies for the verbosity in this example, the definition and usage for the objects in the excerpt above was as follows. This was adapted from an unpublished test case.  
>   
> #include <boost/asio.hpp>  
> #include <boost/cobalt.hpp>  
> #include <boost/process.hpp>  
>   
> namespace cobalt = boost::cobalt;  
> namespace asio = boost::asio;  
> namespace v2 = boost::process::v2;  
> namespace sys = boost::system;  
> using namespace std::literals;  
>   
> cobalt::task<int> test_task_env_set() {  
>   auto exec = co_await asio::this_coro::executor;  
>    
>   // create asio pipes for subprocess output and error  
>   asio::writable_pipe out_wr{exec}, err_wr{exec};  
>   asio::readable_pipe out_rd{exec}, err_rd{exec};  
>   
>   boost::asio::connect_pipe(out_rd, out_wr);  
>   boost::asio::connect_pipe(err_rd, err_wr);  
>   
>   // create the process launcher  
>   v2::default_process_launcher launcher{};  
>   
>   // stdio initializer for a subprocess  
>   v2::process_stdio stdio_init{nullptr, out_wr, err_wr};  
>   // an initializer for environment variables in a subprocess  
>   // sets the environment variable "THUNK" to the value "THUNK" in the subprocess  
>   v2::process_environment pe{"THUNK=THUNK"};  
>   
>   // using the BASH shell for purpose of brevity  
>   auto const bash_cmd = v2::environment::find_executable("bash");  
>   
>   // bash shell script for test.  
>   // fail with error output if THUNK is unset,  
>   // or write the value of THUNK to stdout on success  
>   std::initializer_list<std::string> args = {  
>       "-c", "if [ -v \"THUNK\" ]; then echo \"THUNK=${THUNK}\"; else echo \"THUNK is "  
>             "unset\" 1>&2; exit 1; fi"};  
>   
>   // create the subprocess  
>   sys::error_code ec{};  
>   auto process =  
>       launcher(exec, ec, bash_cmd, args, std::move(pe), std::move(stdio_init));  
>   
>   BOOST_ASSERT(ec.value() == 0);  
>     
>   // asynchronous wait for subprocess exit  
>   auto [proc_ec, exit_code] = co_await cobalt::as_tuple(process.async_wait(cobalt::use_op));  
>   
>   // test case follows. the cancellation is applied below  
>   
>   size_t constexpr buffer_size = 64;  
>   char buff_data[buffer_size] = "";  
>   asio::mutable_buffer m_buff = asio::buffer(buff_data);  
>   
>   // test stdout from the exited subprocess, with timeout  
>   //  
>   auto const [out_ec, n_out] = co_await cobalt::as_tuple(  
>     out_rd.async_read_some(  
>       std::forward<asio::mutable_buffer>(m_buff),  
>       asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
>   BOOST_ASSERT(out_ec.value() == 0);  
>   BOOST_ASSERT(n_out > 0);  
>   
>   boost::string_view s_out{buff_data, n_out - 1};  
>   BOOST_ASSERT(s_out == "THUNK=THUNK");  
>   
>   // clear the buffer and test stederr from the exited subprocess, with timeout  
>   //  
>   memset(buff_data, 0, buffer_size);  
>   auto const [err_ec, n_err] = co_await cobalt::as_tuple(  
>     err_rd.async_read_some(  
>       std::forward<asio::mutable_buffer>(m_buff),  
>       asio::bind_executor(exec, asio::cancel_after(100ms, cobalt::use_op))));  
>   
>   // when there's no error output, the read would be cancelled  
>   BOOST_ASSERT(err_ec.value() == asio::error::operation_aborted);  
>   BOOST_ASSERT(n_err == 0);  
>   
>   co_return exit_code;  
> }  
>   
> cobalt::main co_main(int, char*[]) {  
>   asio::io_context context;  
>   auto f = cobalt::spawn(context, test_task_env_set(), asio::use_future);  
>   context.run();  
>   co_return f.get();  
> }  
> If the linker details could be of interest, I've compiled this using cmake, linking with Boost::asio, Boost::cobalt, Boost::process, Boost::system, Boost::filesystem, and Boost::thread libraries.  
>   
> In this example, using BOOST_ASSERT in place of the original Catch2 REQUIRE calls, the absence of output from the test program would indicate success ....  
>   
> I hope that it might provide any example for using [`asio::cancel_after`](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_after.html). Of course, there's also [`cancel_at`](https://www.boost.org/doc/libs/1_88_0/doc/html/boost_asio/reference/cancel_at.html)  
  
well that completion handler cancels  based on a timer, whereas for my requirement the coroutine should pend on the background and resume until set_result() is called or a cancellation due signals. My intention was to simulate the python awaitable  [future](https://docs.python.org/3/library/asyncio-future.html#asyncio.Future)

---

## Comment 12

> Username: pgit  
> Created at: 2026-02-15 15:51:15 UTC  
> Url: https://github.com/boostorg/cobalt/issues/217#issuecomment-3904720205  

```c++  
auto const [err_ec, n_err] = co_await cobalt::as_tuple(err_rd.async_read_some(  
      std::forward<asio::mutable_buffer>(m_buff),  
      asio::cancel_after(100ms, cobalt::use_op)));  
```  
  
Just stumbled over this old issue by accident. `as_tuple` is a completion token adapter, but you are applying it to the result of whatever awaitable type `async_read_some()` returns.  
  
I think you are supposed to apply the completion token adapter to the completion token instead, along the lines of  
  
```c++  
auto const [err_ec, n_err] = co_await err_rd.async_read_some(  
       std::forward<asio::mutable_buffer>(m_buff),  
       cobalt::as_tuple(asio::cancel_after(100ms, cobalt::use_op)));  
```
