# #207 - Make boost::asio::dispatch() decide running vs scheduling based also on the current callstack usage [Closed]

> Username: traceon  
> Created at: 2019-02-25 00:44:17 UTC  
> Updated at: 2020-12-30 01:00:13 UTC  
> Closed at: 2020-12-30 01:00:12 UTC  
> Url: https://github.com/boostorg/asio/issues/207  

There can be recursion when using dispatch() call, which will lead to stack overflows, if scheduler always decides to run the handler instead of scheduling it.  
  
Is this a known issue, or is there any solution/workaround for this? (Or this is intentionally left for the user to care about?)  
  
```  
The io_context guarantees that the handler will only be called in a thread in which  
the run(), run_one(), poll() or poll_one() member functions is currently being invoked.  
The handler may be executed inside this function if the guarantee can be met.   
```  
...I am asking this, because the doc says it **may** be executed, but not **will** be executed if the guarantee can be met, assuming the executor is given a right to still postpone the call.  
  
A couple of possible workarounds that come to my mind, if I were going to implement my own Executor, with blackjack and hookers:  
- manually specifying a maximum allowed level of nesting in dispatch() (requires user-provided value)  
- manually specifying a limit of a callstack part between io_context::run() or the first dispatch() and last dispatch() in bytes, that, if exceeded, will tell the next dispatch() to enqueue for later (requires user-provided value)  
- automatically detecting the current callstack usage (like some `abs(curr - begin) / stack_size`), and, if it is bigger than some fixed % or the space left is smaller than the average of the previous dispatch-to-dispatch increments, tell the next dispatch() to enqueue for later (can be done automatically, I guess?)

---

## Comment 1

> Username: youngwolf-project  
> Created at: 2019-02-25 14:11:48 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-467025104  

I met this behavior before, and I feel it's my fault, which means I believe it's by design.  
But I agree that your solutions are better, I also considered to limit the call depth by my own codes.

---

## Comment 2

> Username: djarek  
> Created at: 2019-02-27 23:56:01 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468079035  

You should only call `dispatch()` if you know you won't end up with (potentially) unbounded recursion. In the context of asynchronous operations, `dispatch()` is only allowed in a continuation.

---

## Comment 3

> Username: traceon  
> Created at: 2019-02-28 11:19:38 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468236315  

> In the context of asynchronous operations, `dispatch()` is only allowed in a continuation.  
  
Is it mentioned anywhere in the docs?

---

## Comment 4

> Username: djarek  
> Created at: 2019-03-01 12:44:48 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468653236  

[Here](https://www.boost.org/doc/libs/1_69_0/doc/html/boost_asio/reference/asynchronous_operations.html):  
  
>If an asynchonous operation completes immediately (that is, within the thread of execution calling the initiating function, and before the initiating function returns), the completion handler shall be submitted for execution as if by performing ex2.post(std::move(f), alloc2). Otherwise, the completion handler shall be submitted for execution as if by performing ex2.dispatch(std::move(f), alloc2).

---

## Comment 5

> Username: traceon  
> Created at: 2019-03-01 17:43:25 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468749051  

@djarek thank you!  
  
However, it doesn't look like any of `dispatch()` calls within the library itself is obeying that rule (especially in tests), so another question arises: what are examples of those asynchonous operations for which I would be allowed to call `dispatch()`?  
  
From the wording above, any use of `dispatch()` is not legitimate unless it is performed as a tail call (which you can't guarantee in C++.)  
  
Am I missing something?

---

## Comment 6

> Username: djarek  
> Created at: 2019-03-01 18:10:19 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468757391  

`dispatch()` is never invoked outside of continuations in ASIO, at least not in asynchronous operations (obviously, executor tests can call them in any context). Note that tail-call is not the same as a continuation, in simple terms a continuation is execution of a function in a (potentially different) thread of execution, after the original thread of execution called `init.result.get()` in the initiation function.

---

## Comment 7

> Username: traceon  
> Created at: 2019-03-01 19:39:10 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468786412  

Then, why `asio::dispatch()` doesn't consult `asio_handler_is_continuation()` when deciding if it is going to run or schedule?

---

## Comment 8

> Username: djarek  
> Created at: 2019-03-01 22:00:38 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-468826859  

Because `asio_handler_is_continuation` is not a part of Networking TS and Executors have a different way of being informed "this is a continuation". If `dispatch()` checked `asio_handler_is_continuation` it would be a pessimization for types that don't define `asio_handler_is_continuation`, e.g. closure types produced by lambda expressions.

---

## Comment 9

> Username: traceon  
> Created at: 2019-03-03 22:24:29 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-469071245  

I see. But what if you "submitting" a continuation from a loop of the function (i.e., effectively, you want the scheduler to dispatch the next iteration, without exiting the current iteration)? Looks like the same problem will happen here?

---

## Comment 10

> Username: ghost  
> Created at: 2020-12-30 01:00:11 UTC  
> Url: https://github.com/boostorg/asio/issues/207#issuecomment-752291202  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#675](https://github.com/chriskohlhoff/asio/issues/675).
