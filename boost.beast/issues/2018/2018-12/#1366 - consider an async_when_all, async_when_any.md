# #1366 - consider an async_when_all, async_when_any [Closed]

> Username: vinniefalco  
> Created at: 2018-12-12 05:15:53 UTC  
> Updated at: 2019-04-19 03:14:53 UTC  
> Closed at: 2019-04-19 03:14:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1366  

This could be interesting to implement and might help with timers

---

## Comment 1

> Username: madmongo1  
> Created at: 2018-12-12 17:28:45 UTC  
> Updated at: 2018-12-12 17:30:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-446672679  

+1 for this. It would bridge the gap between today (no coroutines) and 2023 (maybe coroutines).  
possible syntax:  
  
When suggesting this I envisaged the idea of a "future tied to an executor" It wouldn't be a real future as there would be no need for locking (asio providing its own determinism via strands).  
  
given:  
  
```  
auto future1 = async_something(..., beast::async_future(my_strand));  
auto future2 = async_something_else(..., beast::async_future(my_strand));  
  
beast_async_await_one_of(move(future1), move(future2), [](auto f1, auto f2)  
{  
  if (f1)  
  {  
    // f1 has fired, f2 therefore has not. It might be that the handler has been invoked, but this  
   // handler's call with a valid f2 will not happen. This eliminates crossing cases  
    if (auto&ec = get<error_code>(f1) ; is_error(ec))  
      handler_event_1_error(ec);  
    else  
      handle_event1_success(std::get<std::size_t>(f1));  // bytes read  
  }  
  else  
  {  
    assert(bool(f2));  
    ... similar for async op 2  
  }  
  
});  
  
// variants  
  
template<class...Futures>  
auto async_await_any(Futures&&...);  // called when any of the futures has state, does not suppress crossing cases  
template<class...Futures>  
auto async_await_one(Futures&&...);  // called when one of the futures has state, suppresses all others - even if the handlers are in flight within the strand  
template<class...Futures>  
auto async_await_all(Futures&&...);  // called only when all of the futures has state  
  
// also, variable sized versions  
  
template<class FutureSequence = std::vector<polymorphic_async_future>>  
auto async_await_one(FutureSequence&&) .  // called when one of the futures has state, suppresses all others   
...etc  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-12 17:53:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-446680986  

```  
auto future1 = async_something(..., beast::async_future(my_strand));  
```  
Is the asynchronous operation launched immediately or is it launched when awaited (e.g. via `async_await_any` in your example)?

---

## Comment 3

> Username: madmongo1  
> Created at: 2018-12-12 19:39:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-446717016  

Thinking off the top of my head, it would be analagous to any existing async operation who's handler is bound to a (possibly different) executor.  
  
It starts immediately. It is the completion handler who's invokation is controlled by:  
a) the io object's executor  
b) the executor bound to the handler  
  
In terms of implementation I am thinking that the async_future would be implemented in terms of a composed operation bound to my_strand. This gives it an opportunity to prevent the final handler from "happening" in when there is a crossing case (in the case of async_await_one)

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-12-12 19:43:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-446718423  

> In terms of implementation I am thinking that the async_future would be implemented in terms of a composed operation bound to my_strand  
  
Ah, that would work. However...  
  
> It wouldn't be a real future as there would be no need for locking  
  
...a strand comes with a mutex to synchronize its queue of handlers, something to keep in mind.

---

## Comment 5

> Username: madmongo1  
> Created at: 2018-12-13 02:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-446814615  

>  ...a strand comes with a mutex to synchronize its queue of handlers, something to keep in mind.  
  
This is completely understood. What I mean to say is that any serialisation of access should ideally be taken care of by the executor. The reason here is that currently asio IO_contexts can be set for optimal performance when used in io_context-per-thread mode. It would be a shame to pessimise the await-multiple-things implementation with un-necessary locks.  
Having said this, I can imagine a trait, or perhaps auto-selection baased on the current mode of the io_context/executor.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-04-19 03:14:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1366#issuecomment-484754695  

I just don't see how to implement this in a general way that has a clean interface. My prototypes all end up looking like war casualties and not something that I want to put into a public interface.
