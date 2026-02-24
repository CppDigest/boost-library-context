# #111 - Proposal: New launch policy for eagerly evaluated synchronous future continuations [Closed]

> Username: HadrienG2  
> Created at: 2017-01-12 11:06:59 UTC  
> Updated at: 2017-12-21 17:40:28 UTC  
> Closed at: 2017-12-21 17:40:28 UTC  
> Url: https://github.com/boostorg/thread/issues/111  

From a conceptual point of view, future continuations are awesome. They allow the dynamic definition of a processing pipeline, where multiple asynchronous operations are chained transparently without ever blocking for any of them until doing so is actually required.  
  
However, I am finding myself in a situation where none of Boost's continuation launch policies seem to fit, in the sense that all of them result in bad performance or unnecessarily convoluted code. And I would like to propose another launch policy which could address that.  
  
Consider the following processing pipeline, which is a heavily simplified form of my processing chain where future continuations are a attached in several places throughout the codebase.  
  
    auto future = requestStorage()  
                  .then( loadInputs )  
                  .then( executor, userProvidedFunction )  
                  .then( writeOutput );  
  
In this processing pipeline, requestStorage queues an allocation request to a bounded storage mechanism, which will be honored once some storage becomes available. At this point, the future returned by requestStorage will be set with a handle to the corresponding storage location. When that happens, I want to load inputs from some IO resource into the storage system, then call a user-provided function which operates on those inputs in a CPU-intensive manner, and finally write down the outputs. At the end, I get a future<void> or similar that I can use to tell when the whole process has completed.  
  
Now, my goal is to run this processing pipeline for a bunch of user requests without spawning a large number of OS threads, because I know the later to kill my task scheduling performance. How do I do that?  
  
- Implementing requestStorage without threads is easy, all I need is a way to queue unfunfilled storage requests, and to be notified when storage is liberated. I can set a promise with a handle to that storage when that happens, and that will trigger the remainder of the processing pipeline.  
- For loadInputs, I may want to use asynchronous IO, but that does not mean that I'll have to spawn one thread per IO request. Instead, I can just submit a bunch of IO requests to a number of centralized IO classes, get futures in return, and return the union of these futures (as computed by boost::when_all) as my result. Future unwrapping will then ensure that my result future is ready once the union of IO futures is ready.  
- I don't know what the user-provided function is doing, but I know it to be cpu-intensive, so I'm going to schedule it on some multithreaded executor in order to leverage all those CPU cores that I paid for. Because those executors us a thread pool pattern, I can be confident that no extra thread will be spawned as a result of this.  
- Finally, writeOutput can be structured like loadInputs: start asynchronous IO, get futures in return, and return the union of these IO futures as a result.  
  
So, it is possibly to implement every piece of work in my processing chain with a constant number of threads and a tiny bit of scheduling work here and there. The problem is that the default behaviour of Boost's future continuations is to spawn all of that scheduling work in extra threads, resulting in a huge number of OS threads being spawned and destroyed per request.  
  
To quite GDB on this matter:  
  
    [New Thread 0x7ffff7f97700 (LWP 6365)]  
    [New Thread 0x7ffff6794700 (LWP 6366)]  
    [Thread 0x7ffff7f97700 (LWP 6365) exited]  
    [New Thread 0x7ffff6f95700 (LWP 6367)]  
    [Thread 0x7ffff6794700 (LWP 6366) exited]  
    [Thread 0x7ffff6f95700 (LWP 6367) exited]  
    [New Thread 0x7ffff7f97700 (LWP 6369)]  
    [New Thread 0x7ffff6f95700 (LWP 6370)]  
    [Thread 0x7ffff7f97700 (LWP 6369) exited]  
    [New Thread 0x7ffff7f97700 (LWP 6371)]  
    [Thread 0x7ffff6f95700 (LWP 6370) exited]  
  
...and, unsurprisingly, looking at performance profiles, pthread_clone ends up being a signficant contributor to my request scheduling overhead.  
  
Why does this happen? Well, my first guess is that future.then()'s default launch policy is boost::launch::async, which spawns an extra thread. Definitely not what I want here. So, what are my other options?  
  
- boost::launch::deferred forces me to call .get() on my output futures in order to start the processing, thusly blocking my scheduling thread. The reason I'm bothering with an asynchronous architecture is that I don't want to do that.  
- boost::launch::executor means that I need to define some executor specifically for my scheduling continuations, which as mentioned previously are pretty short. Overhead aside, this means that I need to declare this executor as a (semi-)global variable or create one for every component that can potentially attach a synchronous continuation to a future.  
- boost::launch::inherit would probably be undefined behaviour here since my original future is set by a promise, and not by an asynchronous task originating from some launch policy.  
  
None of these seem to be very good fit here. Since again, my scheduling work is very small and nonblocking, what I would like to do instead is the following:  
  
- If .then() is called on a ready future, run the continuation immediately.  
- If .then() is called on a non-ready future, schedule the continuation to be run on the thread that will set the associated promise.  
  
An astute reader will notice that this launch policy is essentially a variant of boost::launch::deferred that uses eager evaluation instead of lazy evaluation: we want to run the continuation as soon as the future is ready, and not as soon as its value is requested.  
  
In addition, this launch policy is extremely light on resource requirements: you do not need extra threads, executors, concurrent queues, mutexes or any other kind of heavy-handed infrastructure, you could in principle implement it directly in the future class using nothing but an atomic compare-and-swap in future.then() and an atomic read in promise.set_xyz().  
  
So, what would you think about this proposal?

---

## Comment 1

> Username: HadrienG2  
> Created at: 2017-01-12 12:51:01 UTC  
> Updated at: 2017-01-14 08:53:51 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-272155420  

Looking around further, one possible workaround for the absence of the proposed launch policy is to use a global inline executor. This is what I'm going to use while waiting for your inputs on this proposal.  
  
Here are some thoughts on why it seems less optimal than a dedicated launch policy to me:  
  
- It introduces global state that must be initialized on program startup, which can have a number of unpleasant consequences (e.g. such global state must be used with care in Windows DLLs).  
- Using a mutex to check for closed_ in the inline executor's submission function reduces its scalability in the presence of many threads of execution, each executing many short inline continuations.  
  
The alternative would be to have one inline executor per task which can spawn synchronous continuations, which just feels like code duplication.

---

## Comment 2

> Username: viboes  
> Created at: 2017-01-22 09:23:30 UTC  
> Updated at: 2017-01-22 09:23:51 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-274319455  

Thanks for you suggestion. I'll try to comment soon.

---

## Comment 3

> Username: viboes  
> Created at: 2017-01-22 10:48:09 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-274323395  

You don't need to guess, you can read the documentation.  
  
In order to reduce the cost of threads one alternative is to use Boost.Fiber that cost less to create as they are on the user land.  
  
Another is to use an executor for all those continuation, so that you don't have a new thread for each continuation.  
  
  
> what would you think about this proposal?  
  
It could be interesting to have a launch::sync policy and I believe others have suggested this already (see the boost ML). The question is why do you need to execute the callback synchronously? This will be a little bit intrusive, as you will be able to work on the producer thread.  
Why working on a executor is no enough for you?  
   
I believe that we should follow the C++ standard proposals design. Do you know if there is something like this policy proposed for the standard or something that is missing in Boost.Thread and that will respond to your need?  
  
I have no time to work on this now, but if you find something on the standard proposal that can help you, I will appreciate a PR.  
  
P.S. Note that you have the possibility to add a callback on the shared state.

---

## Comment 4

> Username: HadrienG2  
> Created at: 2017-01-23 06:55:11 UTC  
> Updated at: 2017-01-28 16:39:43 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-274413295  

> You don't need to guess, you can read the documentation.  
  
I did look it up, however the behaviour of calling .then() without specifying a launch policy is (rightfully) undefined.  
  
> In order to reduce the cost of threads one alternative is to use Boost.Fiber that cost less to create as they are on the user land.  
  
I do not think that I can use them in the context of future continuations. To work efficiently, fibers must return control to the scheduler when blocking, and I have not seen a customization point in .then() that would allow me to do so.  
  
> Another is to use an executor for all those continuation, so that you don't have a new thread for each continuation.  
  
This is what I currently do, as the inline executor is pretty close to what I want. However, it forces me to add either a global executor variable or one inline executor member per class which can potentially spawn synchronous continuations. In addition, the executor's check for closed_ on every submission, which is protected by a mutex, may end up being a scalability bottleneck when working with many threads and very short tasks. For these reasons, I think a dedicated launch policy could serve this use case better.  
  
> It could be interesting to have a launch::sync policy and I believe others have suggested this already (see the boost ML). The question is why do you need to execute the callback synchronously? This will be a little bit intrusive, as you will be able to work on the producer thread.  
Why working on a executor is no enough for you?  
  
I'm not sure if we should call it launch::sync, as that would conflict with the previous name of std::launch::deferred in the C++0x draft, a naming convention which I believe Boost still supports when the right combination of flags is set. At least, that's where my attempts at searching this name led me.  
  
I think synchronous callbacks are the right solution in this case, because I want to schedule very tiny pieces of work which are essentially adapters from one part of my processing pipeline to the next. Such small workloads tend to put a lot of stress on the scheduling infrastructure, so the lighter-weight the underlying infrastructure the better. In addition, since these pieces of work are latency-critical, scheduling them directly on the CPU where the data is in cache would be beneficial.  
  
Some concrete examples:  
  
- After allocating a storage resource, I wrap its handle into an RAII object that will free it up on destruction, as a high-level interface that makes it harder for my users to make mistakes.  
- After storage is ready, I start all IO asynchronously.  
- After all IO is complete, I check that all associated futures are non-exceptional before passing the handle to the storage resource down the processing chain.  
  
> I believe that we should follow the C++ standard proposals design. Do you know if there is something like this policy proposed for the standard or something that is missing in Boost.Thread and that will respond to your need?  
  
I have not seen any mention of this yet in the C++ standard proposals that I am aware of, but I did not do a very in-depth search (only looking up the TSs on CPPreference). Where should I look to get the most up-to-date information on what's been proposed?  
  
Longer-term, I would certainly be in favor of proposing such a launch policy for inclusion in the C++ standard, but my experience with standard committees is that they usually want to look at an implementation before accepting a new proposal.  
  
> I have no time to work on this now, but if you find something on the standard proposal that can help you, I will appreciate a PR.  
  
The upcoming week is similarly busy for me, but longer-term I'll see what I can do :)  
  
> P.S. Note that you have the possibility to add a callback on the shared state.  
  
How does this differ from future::then()?

---

## Comment 5

> Username: debdattabasu  
> Created at: 2017-04-19 06:16:32 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-295114747  

The behaviour mentioned by @HadrienG2 is the default behaviour of futures in many languages I have worked with. Java CompletableFutures, Scala Futures, etc. They are also the standard behaviour of facebook's folly futures.   
  
The reason for this is that it allows for better performance as well as having a futures abstraction that can work independent of the underlying async execution model. Since c++ standard does not define the thread used when then is called with no launch policy, we might as well execute the continuation inline. This is consistent with c++ standard, as well as all practical futures implementations in use today.

---

## Comment 6

> Username: viboes  
> Created at: 2017-04-19 20:37:00 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-295430180  

@HadrienG2 Sorry  for being so late. I missed this.  
Please, could you tell me where calling .then() without a policy is undefined?  
  
A dedicated policy that need to use a pool of threads will need a global variable which I don't want to have neither. At least not until the standard adopt it.  
  
If you have a patch for the additional policy you want, I will be interested in looking at it.  
  
You can look at  
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/  
and the preceding years.  
  
Look for Executors and concurrency. If you don't reach I could give you more specific pointers.  
  
Have you read  
http://www.boost.org/doc/libs/1_64_0/doc/html/thread/synchronization.html#thread.synchronization.futures.lazy_futures?

---

## Comment 7

> Username: viboes  
> Created at: 2017-04-19 20:41:02 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-295432048  

@debdattabasu The C++ standard doesn't define what thread to use, but as if a new thread was used.  
  
Any concrete proposal that is backward compatible and doesn't imply a lost of the current performances is welcome. If the backward compatibility cannot be  ensured the alternative been to have a new Boost.Thread V5 :( which I have no time today to work on. This doesn't mean that someone else can not do it.

---

## Comment 8

> Username: debdattabasu  
> Created at: 2017-04-20 06:53:05 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-295601238  

@viboes   
Hi. Thank you for taking the time to respond. My proposal is this: Whenever then() is called with launch::none, the continuation should be called inline. This may be on the thread that calls promise.set_value, or the thread that calls then() depending on when then() is called. In short, execute the continuation immediately on completion.  
  
  
 Right now the boost documentation says:  
  
-> When the launch policy is launch::none the continuation is called on an unspecified thread of execution.  
  
Since the thread is unspecified, we can choose any thread we want and still be backwards compatible with the existing library. This should not be a compatibility issue according to docs. :)

---

## Comment 9

> Username: HadrienG2  
> Created at: 2017-05-03 10:10:05 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-298871428  

@viboes I had a look at the [lazy future](http://www.boost.org/doc/libs/1_64_0/doc/html/thread/synchronization.html#thread.synchronization.futures.lazy_futures) documentation. These are pretty close in spirit to the std::launch::deferred launch policy, which allows for lazy continuation evaluation, in which future continuations are only executed when the host future is awaited. A defining feature of lazy evaluation is that in the following code, the future continuation will never be executed:  
  
```c++  
f.then(std::launch::deferred, continuation);  
while(true) {}  
```  
  
Although lazy evaluation has its use cases, the above behavior is often undesirable. It makes it worryingly trivial to build code which deadlocks, and it hurts processing latency if the scheduling code takes too much time to reach the associated .get() or .wait() call for any reason, as the evaluation of the continuation will be delayed.  
  
Eager evaluation does not have these problems, which is why it is in most case preferable. With eager evaluation, the only question is which thread should run the continuation. We can spawn one thread per continuation (which is what most std::launch::async implementations do), but that is often wasteful. We can reuse a finite thread pool (which is what HPX and Boost's thread pool executor do), but as you mention doing it as a standard policy requires a global executor object, which may be undesirable. The only solution left is thus to reuse one of the existing threads, either the one which sets the continuation, or the one which sets the value of the futures, whichever comes last. And this is what the proposed inline evaluation policy would do.

---

## Comment 10

> Username: viboes  
> Created at: 2017-08-24 18:49:15 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-324723695  

Hi,  
  
I will have some time to experiment on this.   
  
Do you have a PR that I can test?

---

## Comment 11

> Username: muggenhor  
> Created at: 2017-11-30 12:47:23 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-348178541  

Question: am I missing something or is this (executing continuations in the thread calling .set_value/.set_exception or .then() when finished already) already provided by the `boost::launch::sync` policy?

---

## Comment 12

> Username: viboes  
> Created at: 2017-11-30 18:07:01 UTC  
> Url: https://github.com/boostorg/thread/issues/111#issuecomment-348271724  

Hi,  
  
we will need some test and documentation as well.
