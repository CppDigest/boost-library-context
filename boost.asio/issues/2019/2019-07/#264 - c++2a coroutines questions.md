# #264 - c++2a coroutines questions [Closed]

> Username: KategoryBee  
> Created at: 2019-07-24 04:20:24 UTC  
> Updated at: 2020-12-30 01:07:37 UTC  
> Closed at: 2020-12-30 01:07:36 UTC  
> Url: https://github.com/boostorg/asio/issues/264  

I'm investigating using c++2a coroutines and asio for new code + slowly migrating current callback hell. So far it seems pretty nice to use, but there's a few questions:  
  
---  
  
What's the expected way to launch another coroutine concurrently, then later join with it?  
  
The following code fails to compile (clang 8, boost 1.70):  
  
```cpp  
auto concurrent = co_spawn(ctx, do_something, use_awaitable);  
  
co_await do_something_else();  
co_await std::move(concurrent);  
```  
  
inside `co_spawn` with  
  
```  
boost/asio/impl/co_spawn.hpp:118:10: error: no viable conversion from returned value of type 'typename enable_if<detail::async_result_has_initiate_memfn<const use_awaitable_t<executor> &, awaitable_signature<awaitable<void, executor> > >::value, typename ::boost::asio::async_result<typename ::boost::asio::decay<const use_awaitable_t<executor> &>::type, awaitable_signature<awaitable<void, executor> > >::return_type>::type' (aka 'void') to function return type 'typename ::boost::asio::async_result<typename ::boost::asio::decay<const use_awaitable_t<executor> &>::type, typename detail::awaitable_signature<typename result_of<(lambda at /home/chris/projects/experiment/main.cpp:208:15) &()>::type>::type>::return_type' (aka 'awaitable<void, boost::asio::executor>')  
```  
  
---  
  
Is destroying `awaitable`s that are suspended waiting on an async op, such as `async_connect`, supported?  
  
I'd expect that the operation still runs but the completion gets ignored.  
  
---  
  
It would also be nice if the promise type (`boost::asio::detail::awaitable_handler` ?) wasn't in `detail`, and there was an easy way to just make a pair of that + awaitable for the current coroutine.  
  
The use-case is placing them in a vector for later completion, based on an out-of-order messaging system. Currently we can type-erase it easily through `async_initiate` + `unique_function`, but it seems redundant to do that since all of our code paths would be using coroutines.

---

## Comment 1

> Username: cedral  
> Created at: 2019-08-12 16:31:00 UTC  
> Url: https://github.com/boostorg/asio/issues/264#issuecomment-520497849  

That doesn't look like it makes any sense. Where are you trying to move concurrent to? Are your sure you don't mean  
std::move(co_await concurrent)? Which is how you would move the result of the concurrent operation.

---

## Comment 2

> Username: KategoryBee  
> Created at: 2019-08-12 22:31:29 UTC  
> Updated at: 2019-08-12 22:31:48 UTC  
> Url: https://github.com/boostorg/asio/issues/264#issuecomment-520619361  

It's just because the current `awaitable<>` is move only, and `await_transform` takes it by value, so you gotta move it in to `co_await`. It's kinda weird, but it works.  
  
One would hope `co_await` returns by-value, like `std::future` and friends do.

---

## Comment 3

> Username: cedral  
> Created at: 2019-09-10 17:27:01 UTC  
> Url: https://github.com/boostorg/asio/issues/264#issuecomment-530039586  

Thanks for replying. I see what you are doing now and I actually have a use case for that.

---

## Comment 4

> Username: icpz  
> Created at: 2020-03-28 19:34:17 UTC  
> Url: https://github.com/boostorg/asio/issues/264#issuecomment-605508421  

Hi, is there any workaround?  
I'm trying to implement a count-down-latch thing that works with asio, but I don't know how to... Could you please give some advice?  
  
Sorry to bother you... Thanks!

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 01:07:35 UTC  
> Url: https://github.com/boostorg/asio/issues/264#issuecomment-752292631  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#707](https://github.com/chriskohlhoff/asio/issues/707).
