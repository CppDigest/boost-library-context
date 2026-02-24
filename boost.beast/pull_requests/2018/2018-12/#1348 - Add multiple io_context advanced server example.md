# #1348 Add multiple io_context advanced server example [Closed]

> Username: octobanana  
> Created at: 2018-12-03 23:29:49 UTC  
> Updated at: 2022-06-22 20:08:41 UTC  
> Closed at: 2022-06-22 20:08:41 UTC  
> Url: https://github.com/boostorg/beast/pull/1348  

I had a go at #1285, an advanced server example that uses a single io_context per thread. It is based off the advanced server example. It selects the next io_context per socket in a round robin fashion. If you diff it to the advanced server example, you can see the changes that were made.  
  
Is this kind of what you had in mind?

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-12-04 00:05:39 UTC  
> Updated_at: 2018-12-29 09:21:30 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-443918267  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=h1) Report  
> Merging [#1348](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/f00237cb35bcb0d3d544db4b80de38f1aba78ab0?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1348/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop    #1348   +/-   ##  
========================================  
  Coverage    93.49%   93.49%             
========================================  
  Files          112      112             
  Lines        11031    11031             
========================================  
  Hits         10313    10313             
  Misses         718      718  
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=footer). Last update [f00237c...ca36c46](https://codecov.io/gh/boostorg/beast/pull/1348?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:09:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181040754  

📁 example/advanced/server-io/CMakeLists.txt

```diff
   1 |+ #
   2 |+ # Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:09:03 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238487286  

Should add your name

> Username: octobanana  
> Created_at: 2018-12-05 05:26:28 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238934879  

Alright


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:09:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181040932  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
  39 |+ class round_robin
  40 |+ {
  41 |+     using io_ptr = std::shared_ptr<boost::asio::io_context>;
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:09:52 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238487437  

I prefer to just repeat the type, it is more clear

> Username: vinniefalco  
> Created_at: 2018-12-04 00:19:49 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238489245  

We shouldn't need `shared_ptr` here it should be possible to simply use `vector<io_context>`.

> Username: djarek  
> Created_at: 2018-12-04 09:07:34 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238576384  

`vector` won't work because `io_context` is noncopyable.

> Username: vinniefalco  
> Created_at: 2018-12-04 13:30:11 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238661587  

Ah yes you're right. `deque` works though.

> Username: octobanana  
> Created_at: 2018-12-05 05:27:10 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238934996  

I'll remove the using statements and use the full type.

> Username: octobanana  
> Created_at: 2018-12-05 05:31:25 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238935521  

I'll try it out using a `deque`. Since it doesn't need to share ownership, a `vector<unique_ptr<io_context>>`  also works.

> Username: vinniefalco  
> Created_at: 2018-12-05 13:16:48 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239059331  

That is ugly though, extra allocation and indirection per element.

> Username: djarek  
> Created_at: 2018-12-05 16:32:36 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239140998  

there's no need to resize, so I think `make_unique<io_context[]>(n)` is fine

> Username: vinniefalco  
> Created_at: 2018-12-05 17:05:14 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239154469  

`make_unique` is not C++11. And each element has to be individually constructed since we want to pass arguments.

> Username: djarek  
> Created_at: 2018-12-05 22:27:10 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239262120  

https://godbolt.org/z/ZZynWW

> Username: vinniefalco  
> Created_at: 2018-12-05 22:32:09 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239263460  

How do you pass constructor arguments?

> Username: djarek  
> Created_at: 2018-12-05 22:43:30 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239266564  

There's no point of having more than 1 thread per `io_context` if you have load balancing across multiple `io_context`s., so a hardcoded concurrency hint of 1 is fine.

> Username: octobanana  
> Created_at: 2018-12-07 00:22:24 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239662100  

Would there ever be a scenario where having several threads per multiple `io_context`'s would be beneficial? Say 2 `io_context`'s and 4 `thread`'s, 2 per context?

> Username: vinniefalco  
> Created_at: 2018-12-07 00:30:49 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239663405  

I don't think so. And if there is, then it is such a niche use-case that I don't think it is worth addressing in this particular class.


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:10:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181041035  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
  66 |+ public:
  67 |+ 
  68 |+     round_robin(std::size_t size = 1)
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:10:22 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238487545  

Should be `explicit`

> Username: octobanana  
> Created_at: 2018-12-05 05:34:55 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238935917  

Right, I'll add `explicit`


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:12:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181041505  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
 118 |+         return io;
 119 |+     }
 120 |+ }; // class round_robin
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:12:42 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238487965  

I would put this class in its own header and rename it to `multi_io_context`. This is a candidate for the _experimental directory.

> Username: octobanana  
> Created_at: 2018-12-05 05:36:17 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238936057  

Alright, I'll move it to its own header and rename it.


---

## Review 6 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:14:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181042022  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
 910 |+ 
 911 |+     // (If we get here, it means we got a SIGINT or SIGTERM)
 912 |+ 
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:14:55 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238488388  

I think it would be nice to explicitly call `io_pool.join()` this way we can place a comment after that says "at this point, no more threads are running" or similar. Otherwise we have this invisible behavior occurring in `~round_robin()`.

> Username: octobanana  
> Created_at: 2018-12-05 05:40:37 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238936653  

That makes sense. I'll move the thread join calls from `run()` to it's own `join()` function.


---

## Review 7 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:16:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181042276  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
 887 |+     // The io_context is required for all I/O
 888 |+     // Use one io_context per thread round robin style
 889 |+     round_robin io_pool {threads};
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:16:07 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238488589  

I think this would look better like this:  
```  
multi_io_context ioc{threads};  
```

> Username: octobanana  
> Created_at: 2018-12-05 05:42:06 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238936870  

Sure


---

## Review 8 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:17:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181042476  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
 109 |+ 
 110 |+     boost::asio::io_context&
 111 |+     get()
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:17:04 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238488756  

Consider calling this function `next_context` to make it clear that 1. a choice is being made, and 2. the state of the object is changing

> Username: octobanana  
> Created_at: 2018-12-05 05:45:08 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238937272  

Using `next_context()` should make it more clear in what it does.


---

## Review 9 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:17:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181042642  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
  90 |+         {
  91 |+             threads.emplace_back(
  92 |+                 boost::bind(&boost::asio::io_context::run, e.io));
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:17:53 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238488883  

I think we can use `std::bind`. Better yet, this should probably be a lambda since it will be more clear to readers.

> Username: djarek  
> Created_at: 2018-12-04 09:40:34 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238587709  

In general you should avoid taking the address of member functions of classes you don't own. (Especially if they're in they're standard/"semistandard")

> Username: octobanana  
> Created_at: 2018-12-05 05:47:08 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238937521  

I'll change it to use a lambda. Noted on avoiding taking the address of member functions of non-owned objects.


---

## Review 10 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:19:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181042942  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
  37 |+ namespace websocket = boost::beast::websocket;  // from <boost/beast/websocket.hpp>
  38 |+ 
  39 |+ class round_robin
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:19:18 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238489142  

Something to consider for the future, not important now:  
```  
template<class SelectionPolicy = round_robin>  
class multi_io_context;  
```

> Username: octobanana  
> Created_at: 2018-12-05 05:55:32 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238938615  

That would be neat. Having a `least_connected` selection policy would be interesting. Give each io_context a weight based on the current number of sockets it's being used with, then next_context would return the io_context with the lowest weight.

> Username: vinniefalco  
> Created_at: 2018-12-05 13:20:57 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239060589  

Hmm good idea! That requires counting the sockets but we could build some RAII counter thingy that you put in your session object. Here's another idea, in the io_context thread we call `run_one` instead of `run`, in a loop, and we can count I/O and compare how much time each thread is spending as a cheap proxy for utilization.

> Username: djarek  
> Created_at: 2018-12-05 22:48:46 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r239267988  

Note that `run_one` may execute more than one CompletionHandler if the `io_context`'s Executor is wrapped with another Executor, e.g. `boost::asio::strand`.


---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2018-12-04 00:26:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1348#pullrequestreview-181044507  

📁 example/advanced/server-io/advanced_server_io.cpp

```diff
  72 |+         for (std::size_t i = 0; i < size; ++i)
  73 |+         {
  74 |+             io_ptr io {std::make_shared<boost::asio::io_context>()};
```

> Username: vinniefalco  
> Created_at: 2018-12-04 00:26:15 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238490471  

There's a magical constant you can pass to `io_context` ctor which gives the best possible single-threaded performance, we should use that. Perhaps eventually make it configurable:  
https://github.com/boostorg/asio/blob/fbe86d86b1ac53e40444e5af03ca4a6c74c33bda/include/boost/asio/detail/concurrency_hint.hpp#L21

> Username: octobanana  
> Created_at: 2018-12-05 06:09:44 UTC  
> Updated_at: 2018-12-29 09:05:13 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#discussion_r238940678  

Ah good idea!


---

## Comment 12

> Username: octobanana  
> Created_at: 2018-12-05 06:10:49 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-444371005  

Thanks for the review and tips! I'll have a go at the changes.

---

## Comment 13

> Username: vinniefalco  
> Created_at: 2018-12-05 13:23:12 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-444483374  

There needs to be a way for the caller to control the thread, or else how would they put a try/catch around the call to `run`? Or what if they want to do some fancy stuff? Or what if they don't even want to use `std::thread` but rather some other system API? We don't have to address this now but the constructor probably needs to accept a "factory" lambda which, when invoked, spawns the thread and invokes the appropriate io_context run or poll functions. Then the multi_io_context calls the factory for each requested io_context.

---

## Comment 14

> Username: vinniefalco  
> Created_at: 2018-12-21 21:23:23 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-449501734  

Is there an update?

---

## Comment 15

> Username: octobanana  
> Created_at: 2018-12-25 00:56:43 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-449781899  

There is! Sorry about the delay. I'll push the updated request tomorrow.

---

## Comment 16

> Username: vinniefalco  
> Created_at: 2018-12-25 01:13:45 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-449782925  

There's no rush. I am about to update all the examples to use `multi_socket`.

---

## Comment 17

> Username: octobanana  
> Created_at: 2018-12-29 08:54:54 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-450477138  

After trying out a couple different implementations for `multi_io_context`, it seemed that it would make more sense if it didn't manage the threads. Removing the threads from the class fixes the lack of access to the `io_context` at thread construction, and allows any thread implementation to be used. What do you think?  
  
There are currently four ways to access the io_contexts, `multi_io_context::get_context()`, `multi_io_context::next_context()`, `multi_io_context::bump_context()`, and `multi_io_context::contexts()`. For the ability to iterate over the `io_contexts`, such as in the on signal handler, I added `multi_io_context::contexts()`, which returns a `std::vector<boost::asio::io_context*> const`. Maybe this could be done better?  
  
Adding `operator[]` and `at()` for random access and `begin()`, `end()` type functions for range-based for loop compatibility would be nice to create a standard-like interface.

---

## Comment 18

> Username: stale[bot]  
> Created_at: 2019-01-29 01:14:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-458367574  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 19

> Username: vinniefalco  
> Created_at: 2019-02-04 01:29:17 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-460108301  

Rebased to just past 209: https://github.com/vinniefalco/beast/tree/multi-io

---

## Comment 20

> Username: octobanana  
> Created_at: 2019-02-13 02:32:21 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-463030489  

Hey, just checking in to see if you've had the chance to look over the changes made since the initial review.

---

## Comment 21

> Username: vinniefalco  
> Created_at: 2019-02-13 04:18:02 UTC  
> Url: https://github.com/boostorg/beast/pull/1348#issuecomment-463051328  

ermmm... yes, and I rebased it (see https://github.com/vinniefalco/beast/tree/multi-io)

---
