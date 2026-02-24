# #115 - The notion of spawning is underdocumented [Closed]

> Username: akrzemi1  
> Created at: 2023-09-17 15:13:14 UTC  
> Updated at: 2023-10-16 15:04:17 UTC  
> Closed at: 2023-10-16 15:04:17 UTC  
> Url: https://github.com/boostorg/cobalt/issues/115  

The only thing that docs say about spawning is:  
  
1. Promises can also be used to spawn tasks easily.  
2. This allows to spawn promised with a simple `+my_task()` expression.  
3. Unlike a [promise](https://klemens.dev/async/reference.html#promise), a task can be awaited or spawned on another executor than it was created on.  
4. Promises are mainly used to spawn tasks easily. (in the description of `detached`)  
5. The short overview of `async/spawn.hpp`:  
  
The spawn functions allow to use [task](https://klemens.dev/async/reference.html#task) directly with asio:  
  
```  
auto spawn(                            task<T>    && t, CompletionToken&& token);  
auto spawn(asio::io_context & context, task<T>    && t, CompletionToken&& token);  
auto spawn(Executor executor,          task<T>    && t, CompletionToken&& token);  
```  
  
Spawn will post both ways, so it is safe to use task to run the task on another executor and consume the result on the current one with [use_op](https://klemens.dev/async/reference.html#use_op).  
  
---  
  
Is this the same concept when you call `+my_promise()` and when you call `async::span`? Is "detach" a synonym for "span"? If so, please have the docs use one term consistently. If not, please indicate the difference and use two terms for the two things.  
  
`async::spawn` is underdocumented. How do the three overloads differ? Does the second overload indicate `asio::io_context` from Boost.ASIO or standalone ASIO? What is the `Executor` in the third overload? What else do you expect to be passed other than `asio::io_context`? What does the first overload do when it is given no executor?  
  
What does it mean that "spawn will post both ways"?  
  
"It is safe to use task to run the task on another executor and consume the result on the current one with [use_op](https://klemens.dev/async/reference.html#use_op)." -- I find it difficult to understand what that means. Could the docs give an illustration of that statement?  
  
"The caller needs to make sure that the executor is not running on multiple threads concurrently, e,g, by using a single-threaded context." -- how do I do that? Is passing `BOOST_ASIO_CONCURRENCY_HINT_1` enough? I don't think so, given that it says "hint".  
  
"The spawn functions allow to use [task](https://klemens.dev/async/reference.html#task) directly with asio". -- What does "directly" mean here? The entire Boost.Async uses asio under the hood. How is `spawn` different? Also, how can we say that the first overload of `span` uses asio directly, when it doesn't mention any executor?
