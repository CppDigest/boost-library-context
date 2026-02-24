# #2931 - less use of auto in examples [Open]

> Username: vinniefalco  
> Created at: 2024-09-19 10:52:47 UTC  
> Updated at: 2024-09-19 20:00:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2931  

Examples should not use `auto` when showing how to do important things, such as here:  
  
https://github.com/boostorg/beast/blob/develop/example/http/server/awaitable/http_server_awaitable.cpp#L253

---

## Comment 1

> Username: sehe  
> Created at: 2024-09-19 13:48:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2361042730  

TBF I think the `auto ex = co_await this_coro::executor;` should not be "improved", since it hides the fact that the awaitable can be instantiated with a specific executor type. In the simplistic approach of just spelling out `asio::any_io_executor` it causes (a) people not to notice it's actually a dependent type (b) automatic create the habit of performance pessimization by creating the type-erasure and ref-counting overhead associated with it.  
  
Yes, the `auto x = T{a,b,c};` instead of just `T X {a,b,c};` can die. I feel that's a cosmetic trend on the same level as aligning assignments or declaration (it's the only appeal).

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-09-19 17:13:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2361733777  

I want to see the type in the example though. Is it using `any_io_executor`? I don't know. I would like to. Although I don't want to construct one needlessly... what is the actual type?

---

## Comment 3

> Username: sehe  
> Created at: 2024-09-19 18:30:58 UTC  
> Updated at: 2024-09-19 18:33:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2361894592  

Yeah that's the thing. It's actually `decltype(co_await this_coro::executor)`. It depends on the awaitable/promise. And that's encoded in the coro return type (thank C++ standards). You really *do not* want to know the type here. It's bad enough that the default be `any_io_executor`. No need to instill that as an inescapable pit-of-doom in the examples. If you must, create a global typedef for it?  
  
```c++  
// using Executor = asio::any_io_executor;  
using Executor = asio::thread_pool::executor_type;  
  
asio::awaitable<void, Executor> my_coro() {  
    Executor      my_executor = co_await asio::this_coro::executor;  
    tcp::acceptor acc(my_executor, {{}, 8989});  
    while (true) {  
        auto strand = make_strand(my_executor);  
        tcp::socket sock   = co_await acc.async_accept(strand /*, asio::deferred*/);  
        asio::co_spawn(strand, my_session(std::move(sock)), asio::detached);  
    }  
}  
  
//     asio::thread_pool ioc(3);  
//     asio::co_spawn(ioc, my_coro(), asio::detached);  
  
```  
  
However, looking one step further along that road, you would get things like  
  
```c++  
asio::awaitable<void, asio::strand<Executor>> my_session(tcp::socket socket) {  
    asio::strand<Executor> my_executor = co_await asio::this_coro::executor;  
    // TODO  
    co_return;  
}  
```  
  
This is why I think it's splendid idiomatic code to deduce the local executor type from the coro's promise.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-09-19 19:36:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2362030634  

Yes I see your point. I agree that it is idiomatic, but example code sometimes needs to prioritize explanatory power over the use of idioms. In this case I want the reader to know whether or not type-erasure is happening in this particular example. Naming the type for the variable has problems, but how about instead we prefix that line with a `static_assert` assuring that we are getting the type we expected? This shows the reader what is happening in a way that preserves the idiom.

---

## Comment 5

> Username: sehe  
> Created at: 2024-09-19 19:47:01 UTC  
> Updated at: 2024-09-19 19:48:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2362048490  

Marginally better. Perhaps with a message stating that the executor type is the one declared in the return value's promise type.  
  
```c++  
static_assert(std::is_same_v<decltype(ex), asio::any_io_executor>, "by default asio::awaitable<T> uses the type-erasing executor container");  
```  
  
I truly think stuff of this kind ought to be documented in their place, like here https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/this_coro__executor.html  
  
In short, I think there's introductory documentation (tutorial style) that should explain these things. Once. Not something to contaminate all examples with. Instead, I expect the examples to show good practice, idiomatic code.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2024-09-19 19:49:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2362053275  

> I expect the examples to show good practice, idiomatic code.  
  
That is normally the case, but it is surprising to discover that type-erasing is taking place here. Hiding it is not so great. And you can't expect users to read the entire manual before using the examples.

---

## Comment 7

> Username: sehe  
> Created at: 2024-09-19 19:55:14 UTC  
> Updated at: 2024-09-19 20:00:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2931#issuecomment-2362061675  

Oh I hear you. Like said before "It's bad enough that the default be any_io_executor. No need to instill that as an inescapable pit-of-doom in the examples". I'd love some highlevel documentation about performance considerations of the default executor type.  
  
In other words, while I agree that people need to be instructed/educated about this, I disagree that it should be recurring in all examples.  
  
For balance: as an example of a recurring piece of explanation that I **do** appreciate is this comment:   
  
```c++  
    void  
    run()  
    {  
        // We need to be executing within a strand to perform async operations  
        // on the I/O objects in this session. Although not strictly necessary  
        // for single-threaded contexts, this example code is written to be  
        // thread-safe by default.  
        net::dispatch(ws_.get_executor(),  
            beast::bind_front_handler(  
                &session::on_run,  
                shared_from_this()));  
    }  
```  
  
The reason is that it pre-empts common confusions/oversight by people learning from this example. It **does not** easily lead to significantly sub-optimal code, as opposed to hard-coding `any_io_executor`.  
  
In the case of `co_await this_core::executor`, people who are curious enough *will* look at [the documentation of it](https://www.boost.org/doc/libs/1_86_0/doc/html/boost_asio/reference/this_coro__executor.html). I say, THAT needs to be improved, if only by linking to the Overview article explaining C++20 coroutines in Asio.
