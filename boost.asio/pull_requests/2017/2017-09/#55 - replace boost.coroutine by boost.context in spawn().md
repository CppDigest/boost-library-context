# #55 replace boost.coroutine by boost.context in spawn() [Open]

> Username: olk  
> Created at: 2017-09-30 06:35:46 UTC  
> Updated at: 2023-01-23 18:15:53 UTC  
> Url: https://github.com/boostorg/asio/pull/55  

- boost.context is deprecated (preprocessor warnings)  
- use boost.context instead (call/cc) instead  
- C++11: use callcc()/continuation from boost.context  
- pre-C++11: use private fcontext-API from boost.context

---

## Review 1 [Commented]

> Username: vinipsmaker  
> Created_at: 2018-09-27 20:52:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/55#pullrequestreview-159636628  

📁 doc/overview/spawn.qbk

```diff
  44 | [link boost_asio.reference.CompletionHandler completion handler].
  44 |- This argument determines the context in which the coroutine is permitted to
  45 |+ This argument determines the context in which the contnuation is permitted to
```

> Username: vinipsmaker  
> Created_at: 2018-09-27 20:52:49 UTC  
> Updated_at: 2018-09-27 20:52:50 UTC  
> Url: https://github.com/boostorg/asio/pull/55#discussion_r221073368  

typo here


---

## Comment 2

> Username: jaycelq  
> Created_at: 2019-07-24 11:35:50 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-514595653  

Any update on this feature?

---

## Comment 3

> Username: vinipsmaker  
> Created_at: 2019-07-24 14:48:03 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-514664035  

@jaycelq, you could use an alternative completion token that implement the same functionality if you're in a hurry: https://github.com/blinktrade/iofiber ([documentation](https://blinktrade.com/iofiber/)).

---

## Comment 4

> Username: shbrk  
> Created_at: 2019-08-05 07:35:07 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-518121459  

Any update on this feature?

---

## Review 5 [Commented]

> Username: cbodley  
> Created_at: 2019-08-29 23:46:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/asio/pull/55#pullrequestreview-281774273  

📁 include/boost/asio/impl/spawn.hpp

```diff
  89 |-       if (--*ready_ == 0)
  90 |-         (*coro_)();
  85 |+         *ec_ = boost::system::error_code();
```

> Username: cbodley  
> Created_at: 2019-08-29 23:46:25 UTC  
> Url: https://github.com/boostorg/asio/pull/55#discussion_r319313441  

*ec_ = ec;


---

## Comment 6

> Username: cbodley  
> Created_at: 2019-08-30 20:59:36 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-526748287  

hi @olk, i worked through the merge conflicts and pushed the result to https://github.com/cbodley/asio/commits/wip-spawn-context. there are a number of new spawn() overloads from the networking ts changes, and the new StackAllocator templates caused some ambiguities that motivated an `is_stack_allocator` template to constrain them

---

## Comment 7

> Username: cbodley  
> Created_at: 2019-11-05 20:49:25 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-550013598  

given that this spawn() interface a) isn't part of the C++ Networking TS and b) has since been superceded in asio by co_spawn(), it seems that there isn't much interest in maintaining or extending it in asio  
  
i ended up forking this spawn() implementation in https://github.com/cbodley/spawn, sans c++98 compatibility, so that it can be used on top of an umodified version of boost::asio (and eventually std::net). many thanks to @olk for this contribution

---

## Comment 8

> Username: loonycyborg  
> Created_at: 2020-12-11 09:43:29 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743090057  

spawn() and co_spawn() use two different kinds of coroutines: stackful vs stackless. Porting between those two is not trivial. If no work is done to upgrade spawn() to new version of coroutine2 then the only option is to deprecate it along with coroutine1 forcing any people who want stackful coroutines to either implement own completion tokens/spawners or use third party ones.

---

## Comment 9

> Username: vinipsmaker  
> Created_at: 2020-12-11 10:56:05 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743125855  

> then the only option is to deprecate it along with coroutine1 forcing any people who want stackful coroutines to either implement own completion tokens/spawners or use third party ones  
  
From experience, I can tell that a serious stackful coroutine token implementation will in no time evolve into a fiber token. I no longer see a point in adding just a stackful coroutine token.  
  
OTOH, if you're using fibers, maybe you should go to something other than Boost.Asio entirely. As Oliver Kowalke explained in <https://wg21.link/p0099>, you can use symmetric coroutines to cut the number of context switches in half, but Boost.Asio execution engine doesn't actually allow you to explore this property (so in the end it only performs as good as asymmetric coroutines).  
  
Unfortunately, Boost.Fiber doesn't play very well with IO (the lack of a cancellation API being the major problem). My current view is that a new IO fiber library should be developed evolving from Boost.Fiber API and adding cancellation (the cancellation API is enough to change semantics and break behaviour, so enough of a motivator to not change current Boost.Fiber) + strands.  
  
But then again, that's a lot of work. I'm personally fine using Boost.Asio with a hand-rolled fiber token. That's way less work and it'll give me integration with the few Boost.Asio libraries that go to great lengths to implement the whole completion token and executors protocols (not many) and even greater lengths to adapt to the API breakage that happens at every release since the networking TS folks got their claws deep into Boost.Asio.  
  
One of the major selling points in Boost.Asio that can't be easily replicated in new projects is the maturity it got over the years by working around the many quirks that OS-specific APIs have. That's a lot of value already, aye.

---

## Comment 10

> Username: olk  
> Created_at: 2020-12-11 11:04:37 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743129732  

> spawn() and co_spawn() use two different kinds of coroutines: stackful vs stackless. Porting between those two is not trivial. If no work is done to upgrade spawn() to new version of coroutine2 then the only option is to deprecate it along with coroutine1 forcing any people who want stackful coroutines to either implement own completion tokens/spawners or use third party ones.  
  
cbodley has implemented a completion token based on boost.context - I don't know why it hasn't been merged (pull request was provided?.

---

## Comment 11

> Username: loonycyborg  
> Created_at: 2020-12-11 13:12:23 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743184961  

> From experience, I can tell that a serious stackful coroutine token implementation will in no time evolve into a fiber token. I no longer see a point in adding just a stackful coroutine token.  
>   
> OTOH, if you're using fibers, maybe you should go to something other than Boost.Asio entirely. As Oliver Kowalke explained in https://wg21.link/p0099, you can use symmetric coroutines to cut the number of context switches in half, but Boost.Asio execution engine doesn't actually allow you to explore this property (so in the end it only performs as good as asymmetric coroutines).  
  
You're most likely suffering from complexity addiction. I merely want to simplify a mess of handlers in a server using coroutines that spell out what is going on there more clearly. Asymmetric stackful coroutines are just fine for this.  
 Also your link leads to "expired ssl certificate" error.

---

## Comment 12

> Username: vinipsmaker  
> Created_at: 2020-12-11 15:31:55 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743262475  

> You're most likely suffering from complexity addiction.  
  
Not really. There is very little difference between stackful coroutines and fibers. You just lack this understanding. And Boost.Asio execution engine already covers the role of the extra mile that fiber adds: concurrency orchestration. Once you're there, you're just properly exposing Boost.Asio features to the “coroutine” token (and then you have no differences between a fiber token and a coroutine token).  
  
Other than that, you'll only miss a fiber token when you need to tame the concurrency and that only happens on complex projects (the last project I worked with had very peculiar routing requirements, so that's my background here).  
  
> Also your link leads to "expired ssl certificate" error.  
  
Here: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0099r1.pdf

---

## Comment 13

> Username: loonycyborg  
> Created_at: 2020-12-11 15:45:29 UTC  
> Updated_at: 2020-12-11 15:45:55 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743269871  

Only thing that matters to me is ability to suspend while saving context. And do so without using any C++ features not present in C++14. And not have to maintain own token when one is available in asio. I suppose implementing asio::spawn() and asio::yield() directly in terms of boost.context would also achieve this without depending on deprecated coroutine1.

---

## Comment 14

> Username: vinipsmaker  
> Created_at: 2020-12-11 16:13:54 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-743284891  

> Only thing that matters to me is ability to suspend while saving context.  
  
[Been there. Done that.](https://github.com/blinktrade/iofiber) A fiber token will **not** complicate your use case. It's `spawn(...)` + `my_async_whatever(..., yield)` as usual.  
  
Anyway, my initial comment was targeting people putting the implementor hat. It's not surprising that you (the one who only wears the user hat) didn't find it useful. Enough of this thread. Have a good day.

---

## Comment 15

> Username: olk  
> Created_at: 2023-01-23 18:15:53 UTC  
> Url: https://github.com/boostorg/asio/pull/55#issuecomment-1400776153  

Please consider [boost.spawn](https://github.com/olk/spawn)

---
