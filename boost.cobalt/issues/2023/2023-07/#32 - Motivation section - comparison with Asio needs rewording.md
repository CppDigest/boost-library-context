# #32 - Motivation section - comparison with Asio needs rewording [Closed]

> Username: anarthal  
> Created at: 2023-07-24 08:10:39 UTC  
> Updated at: 2023-07-24 09:36:37 UTC  
> Closed at: 2023-07-24 09:36:37 UTC  
> Url: https://github.com/boostorg/cobalt/issues/32  

> Unlike asio::awaitable and asio::experimental::coro, async coroutines are open. That is, an asio::awaitable can only await other asio::awaitable and does not provide coroutine specific synchronization mechanisms. async on the other hand provices channel and different wait types (select, gather etc.) that are optimized for awaitables.  
  
* From this sentence, I get that async coroutines can await asio::awaitables - if it's not the case, a rewording on what "open" means is needed.  
* Typo: provices  
* One may argue that Asio does provide `channel` and `parallel_group`. I'd reword as something like:  
  
> Compared to Asio, async provides synchronization primitives that are easier to use, more feature-rich and more optimized. It can do so because it only targets coroutines, while Asio needs to support a broad range of completion tokens.
