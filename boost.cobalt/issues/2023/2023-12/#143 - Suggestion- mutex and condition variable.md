# #143 - Suggestion: mutex and condition variable [Open]

> Username: bgemmill  
> Created at: 2023-12-05 22:14:04 UTC  
> Updated at: 2025-07-04 11:38:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143  

If we have two threads, and both have cobalt coroutines running, a coroutine may want to access some piece of shared data and would want to block until it became available.  
  
If there are other coroutines ready on that thread, we wouldn't want to use std::mutex or std::condition_variable in this case because that would block the entire thread rather than just that coroutine.  
  
There have been a few efforts in this direction, like [this](https://github.com/jbaldwin/libcoro/blob/main/src/mutex.cpp)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-12-05 22:31:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-1841733122  

I am well aware and have been considering this kind of construct. The issue is that those things will be slightly different than `std::mutex` and thus confusing to users, so I would want something clearly distinct. You'll need to mangle through executors anyhow, why not just use `spawn(,..., use_op)` to get onto the other executor and than use a channel to sync?

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-12-05 22:33:26 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-1841735557  

Forgot to mention - I wrote a whole thing to support this in asio: https://github.com/klemens-morgenstern/sam  
  
I won't be submitting this to boost, so beware that I will probably move `sam` out of the `boost` namespace soon.

---

## Comment 3

> Username: bgemmill  
> Created at: 2023-12-06 02:15:23 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-1841973815  

I'm not sure that the constructs have to be different; boost::fiber solves a similar problem with what feels like the [same mutex interface](https://www.boost.org/doc/libs/1_83_0/libs/fiber/doc/html/fiber/synchronization/mutex_types.html)  
  
What you mention about waking up by poking the remote executor is likely how it would work under the covers. In the other good example of a coroutine mutex that would probably happen [here](https://github.com/lewissbaker/cppcoro/blob/master/lib/async_mutex.cpp#L85)  
  
I've been using boost::fiber for a long while now and having mutex and condition variable in there make it feel like "batteries included", so it felt worth suggesting for cobalt too.

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-05-02 00:53:40 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2089354258  

I am using boost.fiber in a project right now and I am even more sure that it's a bad idea. Treading multi-threading and single-threaded concurrency with the same primitives just leads to massive confusion.  
  
What's wrong with `sam` ?

---

## Comment 5

> Username: voidpointertonull  
> Created at: 2024-05-02 04:09:56 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2089495301  

Which part do you consider a bad idea? Catering to multi-thread use cases might be surely out of scope here, but the desire of well-known synchronization primitives is obvious for anyone running into the problem that having just a single thread doesn't mean the lack of concurrency reordering operations.  
  
Providing well-known names is usually a "selling point" and can be seen in multiple async-focused libraries with users usually being rather happy about being able to adapt a huge chunk of "legacy" code by replacing some names and sprinkling await keywords on code.  
I don't believe that attempting to help the users the way you want here should be within the scope of the project. It's somewhat like the pleas for GUI-based program developers not to block the main thread, and that battle is still ongoing for decades at this point, showing that if you have no way to enforce a limitation with a compiler, it will just go ignored in many cases. The async_mutex name is already distinctive enough, I'd argue that anyone confusing std::mutex with cobalt::mutex would go on to block the executor in other ways too anyway.  
  
One significant issue with sam is what you just mentioned in December: Instead of moving closer to a Boost adoption, it's going to stay just yet another library, and it's common to be rather picky about dependencies in the C++ world.  
The "batteries included" comment sums up the issue. With Asio being lower level, Cobalt was expected to be the higher level "one stop shop" for all asynchronous needs, but it's not there (yet?). An asynchronous mutex implementation is definitely a good example of what would someone desire the first time a concurrency issue is encountered, even if it's not necessarily the most efficient way, just merely a way to get a prototype working quick.

---

## Comment 6

> Username: bgemmill  
> Created at: 2024-05-02 10:26:32 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2090119788  

Thanks for getting back to me on this. Since December I've been using a mutex similar to [this](https://github.com/lewissbaker/cppcoro/blob/master/lib/async_mutex.cpp) to good effect.  
  
What I wanted out of a mutex was the ability for coroutines on the same thread to be able to juggle a mutex between themselves without asio. Lewis Baker's for instance uses a few atomic instructions and coroutine suspension/wakeup only. Scheduling a wakeup of a coro on a different thread is the only place asio needs to be called with this approach.  
  
From my reading of `sam`'s mutex, and please correct me if I'm wrong here, it's both mostly asio based and requires the user to drop out of coroutines to use it; I couldn't find a co_await point on it.  
  
That second part is hard because If I'm in a coroutine, and there are other coroutines ready to go, locking this mutex means that the stack has gone from coroutines to regular calls, and once locked, ready coroutines farther back on the stack won't execute.

---

## Comment 7

> Username: klemens-morgenstern  
> Created at: 2024-05-30 15:11:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2139871155  

> The async_mutex name is already distinctive enough, I'd argue that anyone confusing std::mutex with cobalt::mutex would go on to block the executor in other ways too anyway.  
  
You'd be surprised. You can already use a channel for this though.  
  
```cpp  
cobalt::channel<void> mtx{1};  
co_await mtx.write(); // lock  
  
// essentially try_read  
co_await cobalt::ordered_race(mtx.read(), std::suspend_never()); // unlock  
```   
I guess I could add a `try_read` and `try_write` function. Or maybe a `try_immediate` function. But the batteries are included, you just need to assemble it.  
  
> One significant issue with sam is what you just mentioned in December: Instead of moving closer to a Boost adoption, it's going to stay just yet another library, and it's common to be rather picky about dependencies in the C++ world.  
  
Fair, but I think asio should provide this. Chris thinks like me that channels do that.  
  
> The "batteries included" comment sums up the issue. With Asio being lower level, Cobalt was expected to be the higher level "one stop shop" for all asynchronous needs, but it's not there (yet?). An asynchronous mutex implementation is definitely a good example of what would someone desire the first time a concurrency issue is encountered, even if it's not necessarily the most efficient way, just merely a way to get a prototype working quick.  
  
And most of those prototypes will be broken, because there's a thing that's called a `mutex` that isn't one. You will probably get this right, but loads of people won't. I really like to separate multi-threading from concurrency, so distinguishing concepts (i.e. channels from mutexes) makes it all much more obvious.

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2024-05-30 15:14:31 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2139889920  

> What I wanted out of a mutex was the ability for coroutines on the same thread to be able to juggle a mutex between themselves without asio. Lewis Baker's for instance uses a few atomic instructions and coroutine suspension/wakeup only. Scheduling a wakeup of a coro on a different thread is the only place asio needs to be called with this approach.  
  
Channels *might* do the job then. You can also use them as semaphores.  
  
> From my reading of sam's mutex, and please correct me if I'm wrong here, it's both mostly asio based and requires the user to drop out of coroutines to use it; I couldn't find a co_await point on it.  
  
You can use any asio based thingy when it as an `async_foobar` function by using `cobalt::use_op`.   
  
```cpp  
sam::mutex mtx{co_await cobalt::this_coro::executor};  
co_await mtx.async_lock(cobalt::use_op); // make it awaitable.  
```  
  
> That second part is hard because If I'm in a coroutine, and there are other coroutines ready to go, locking this mutex means that the stack has gone from coroutines to regular calls, and once locked, ready coroutines farther back on the stack won't execute.  
  
That would not be the case when using `sam`.

---

## Comment 9

> Username: bgemmill  
> Created at: 2024-05-30 17:09:54 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2140308538  

Thanks for getting back again.   
  
At the moment, it doesn't look like we could use a cobalt::channel as a mutex because it [looks like channels are thread local](https://www.boost.org/doc/libs/1_85_0/libs/cobalt/doc/html/index.html#channel).  
  
The use case I'm trying to solve is two or more coroutines on two or more threads wanting some resource.  
  
For fibers, boost::fiber::mutex handles this pretty well, and it's not clear that there's been a huge amount of confusion between it and std::mutex from looking at the github issues.  
  
`sam` seems like it could work, although as a non-boost project I'd likely continue using Lewis Baker's implementation if I had to grab an outside dependency because it uses asio only for cross thread wakeups.

---

## Comment 10

> Username: saurik  
> Created at: 2024-07-21 22:50:10 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2241800127  

I am not sure from that documentation if channels are actually local to a thread... I feel like it means to say "even if" they are on one thread? But, even if they aren't, what I definitely am missing is closer to a mutex, and I would personally find it confusing to not then call what I want a mutex, as normally do I have decades of experience dealing with mutexes, but I also happily deal with the namespace collisions of every other word being used by projects like this, including "with" and "join" (both of which are also used in synchronous contexts for similar ideas).  
  
FWIW, while I totally appreciate the goal of channel--I also implemented something similar (though only capable of transferring a single value) in my coroutine stack and get much much more use out of than the raw asynchronous mutex--I still use cppcoro's async_mutex, where my use case is (also) "shared access to a single asynchronous resource". To make it more concrete, sometimes I have situations where two asynchronous tasks need to be run on some resource in the order "1 2, 1 2" and never "1 1 2 2", so--just as I would with synchronous threads--I can wrap them in an async mutex, causing a convenient "(1 2) (1 2)".  
  
I know I can implement a simulation of this by passing a talking stick around using a channel--actually, I am realizing maybe I can't, as while I earlier said I feel OK re-interpreting the "single thread" part of the documentation as sort of a typo, I just realized you only say "two coroutine", so might not support multiple readers... I was, maybe humorously, assuming it would work like a Go channel--but, frankly, at that point, I'd be better off just dropping down to the C++ coroutine task level to implement the mutex myself, as, not only would I expect it to be much more efficient, I feel like stacking these abstractions are more likely to cause me to get it wrong.  
  
(Also: I don't always use ASIO and not only do I never use "executors", I really really don't want to be forced to as it undermines the composability of coroutines... I just want a high-quality / performant raw async mutex that is agnostic to what other libraries or run loops it is being used with; ideally, upon unlocking, it would immediately resume a waiting coroutine on the current thread stack: if I want some form of scheduling, I can--as I do with cocoro--trivially build an abstraction over the mutex to provide such by awaiting a scheduler immediately after the wrapped mutex. Honestly, I just wish cppcoro were both maintained and maybe had been adopted into boost so we could work on hashing out its bugs.)

---

## Comment 11

> Username: saurik  
> Created at: 2024-07-21 23:07:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2241806343  

@bgemmill (Thanks for linking to libcoro, btw; I am in the midst of actively trying to move my last few uses off from cppcoro to something else, and libcoro looks pretty amazing. Reading its documentation, though, it is sad to me that they are willing to sprinkle random uses of LIFO when it theoretically helped throughout... when I found a serous bug in cppcoro's schedulers--which I carefully debugged, documented, and filed years ago, but which Lewis analyzed for a while yet never fixed--I wrote my own, and it was initially LIFO just as that was much more obvious to implement, and it caused me tons of problems as my tasks are all network requests and packets and such, and the semi-random shuffling of tasks resulted in other daemons and algorithms I was interfacing with to get confused or use slower paths; I ended up having to put in the effort to switch to fully-FIFO. Maybe the best route is to avoid these libraries and just always write our own coroutine behaviors.)

---

## Comment 12

> Username: klemens-morgenstern  
> Created at: 2024-07-22 00:10:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2241829122  

Channels are not thread-local but single threaded. They can run on a "logical thread" if you will provided for example by a strand in asio. You will need another primitive (either from sam or sth like asio::experimental::concurrent_channel) for anything multi-threaded.  
  
  
> ideally, upon unlocking, it would immediately resume a waiting coroutine on the current thread stack  
  
How would you do this without an executor? If I call `unlock()` on the primitive the locking coroutine, i.e. the one that called `co_await lock()` needs to be scheduled somewhere. Otherwise you'd need to `co_await unlock()` which would then yield control to the locker, which I can imagine anyone would want.

---

## Comment 13

> Username: saurik  
> Created at: 2024-11-30 23:34:14 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2509477448  

@klemens-morgenstern (Sorry this took so long for me to reply. I have had an "interesting" year.)  
  
So, the way I normally model an asynchronous mutex is that you don't _await_ unlock(), you merely _call_ unlock(), which then immediately calls the continuation of a pending coroutine blocked on the lock. There is no reason to await unlock as it will never block and is not actually an asynchronous action: you need to asynchronously lock a mutex, but you only want to synchronously unlock it. In this way you don't need any fixed notion of an "executor" and the mutex becomes entirely agnostic of the framework in which it executes.

---

## Comment 14

> Username: klemens-morgenstern  
> Created at: 2024-12-01 00:09:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2509485484  

Wouldn't that potentially lead to a stackoverflow due to recursion?

---

## Comment 15

> Username: saurik  
> Created at: 2024-12-01 02:16:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2509528180  

@klemens-morgenstern That can happen; but, this is a building block for a thread coordination primitive that is already dangerous--one that, even if used "correctly", can lead to deadlocks if used without care--and so I am not sure if limiting it to only working within the context of an executor model makes sense? Ideally, one would cause the unlock to occur in tail position (which is quite common even if left to accident), and so the stack just pivots and doesn't grow as you flush the waiter queue.  
  
What is nice about a basic asynchronous mutex is that one can see how to build all these other things out of it, without any extra context switch penalties, including the idea behind an executor: in that case, by adding a trivial wrapper around the mutex that rewrites the async lock() to add, immediately after, an async executor.schedule() to transfer the continuation into the executor and return. A solid mutex (or a semaphore) is extremely general, is well-covered by algorithm education, and very efficient!

---

## Comment 16

> Username: klemens-morgenstern  
> Created at: 2024-12-01 03:09:07 UTC  
> Updated at: 2025-07-04 11:38:55 UTC  
> Url: https://github.com/boostorg/cobalt/issues/143#issuecomment-2509543821  

I am not saying that this kind of solution is wrong for your use-cases but there are way to many gotchas a generic library I fear.  
This is btw. why I kept `cobalt` open - you can just write your own async_mutex and `co_await` it from a `cobalt::promise`. Or use mine, from boost.sam.  
  
```cpp  
while (true)  
{  
  auto v = co_await get_value();  
  co_await mtx.lock();  
  store_value(v);  
  mtx.unlock();  
}  
```  
  
The above overflow, if the mtx is contended and if `get_value` always returns true from `await_ready()`. The only way to assure this doesn't happen is to sprinkle some `co_await schedule_self()` in there.   
  
Secondly you'd have random thread switching going on, which does not work with the cobalt at all.  
  
Furthermore, it's not efficient. You need at least two mutexes here, because `.lock()` needs to queue all waitors.   
That can't go into a lock-free single linked list, because it needs to be double linked to support cancellation. So you need to have a mutex around that list (locking twice) and then the actual mutex. And then you'd have a third around the executor for scheduling of the completion.  
  
`spawn` ing onto another executor would just involve two mutexes locked once each.  
  
I just don't think it's a good solution when there are so many cross-thread synchronization mechanism that are asynchronous available (cobalt::spawn, timers, pipes, asio::concurrent_channel). And it's specially not a good default.
