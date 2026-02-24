# #2401 - c++20 <coroutine> generators for boost beast [Closed]

> Username: LoganGoh  
> Created at: 2022-03-31 05:37:45 UTC  
> Updated at: 2023-04-05 15:38:02 UTC  
> Closed at: 2022-09-24 04:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2401  

I am starting work on a new c++20 project that uses boost beast websocket and http. I would like to use async with coroutines, similar to how async/await works in Python and Rust. However there are a lot of different coroutine implementations for c++, and I would like some advice on which one I should use with beast.   
  
Are there currently any plans for beast to support c++20 coroutines, and has work already started on any experimental c++20 coroutine generators for beast? I know how to write my own, but if somebody else is already working on it I would like to make use of it and perhaps make some contributions, instead of redoing what has already been done. I think it would be good to have an async generator for messages being received over the websocket, possibly also implemented as an infinite sequence that supports std::ranges?   
  
On the other hand perhaps c++20 coroutines are still too new, and I should stick with boost coroutines? I see from the http client coro_ssl example that beast is using boost coroutines instead of coroutines2. Should I stick with boost coroutines even though it has been deprecated, or should I use boost coroutines2 instead?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-03-31 06:06:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2401#issuecomment-1084132133  

If you have C++20 and recent compiler suite, I'd be happy to recommend C++20 coroutines. The asio::awaitable support is pretty good and is well maintained.  
  
e.g.:  
  
`auto result = co_await object.async_something(asio::use_awaitable);`  
  
You can write generators, or you can use the `asio::experimental::channel` as a rendezvous point for multiple streams of events. `asio::steady_timer` also makes the basis of an efficient asynchronous semaphore, whichever coroutine system you choose.  
  
If you have older tooling, then you may want to consider boost::coroutine.

---

## Comment 2

> Username: LoganGoh  
> Created at: 2022-03-31 06:35:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2401#issuecomment-1084153766  

Thank you, yes. I am using c++20 and recent gcc. Is there any good example code for how to connect as a websocket client using asio:awaitable and beast? I looked at the example directory in the master branch and the coro example in there is I believe 3 years old and using boost::coroutine?

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-03-31 06:39:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2401#issuecomment-1084156436  

Here is something I wrote some months ago in both C++17 and C++20  
  
https://github.com/test-scenarios/boost_beast_websocket_echo  
  
I am available most days on the CPPLang slack in the `#beast` channel.  
  
https://cppalliance.org/slack/

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-08-31 01:31:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2401#issuecomment-1232345373  

Is this resolved?

---

## Comment 5

> Username: LoganGoh  
> Created at: 2022-09-01 00:06:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2401#issuecomment-1233573455  

Yes, you can close it please  
  
On Wed, 31 Aug 2022, 9:31 am Klemens Morgenstern, ***@***.***>  
wrote:  
  
> Is this resolved?  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/2401#issuecomment-1232345373>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AIRUGJXYACSUL6E6PYVDHALV32YWHANCNFSM5SEGOKGA>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>
