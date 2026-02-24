# #827 - Support user-defined default context/device [Open]

> Username: rosenrodt  
> Created at: 2019-03-31 11:50:14 UTC  
> Updated at: 2019-04-22 00:18:17 UTC  
> Url: https://github.com/boostorg/compute/issues/827  

Sometimes it is desirable to have CL-accelerated libraries initialize with user context managed by some outside application. But Boost.Compute currently hides global state of `context/device/queue` from users; there is no way to let user override e.g., their own global default context.  
  
Because Boost.Compute already maintains global state of `context/device/queue` inside static functions, I think the best case scenario is being able to assign global CL context (derived from preferred CL compute device) without relying on parsing through pre-defined environment variables which `default_context()` implements.  
  
One of the main benefits is to make direct access of device memory between shared objects/DLLs easily achievable. When some main application initialize two libraries `Foo` and `Bar`, those two libraries also initialize all internal buffers with global user-defined context. That way, data produced by library `Foo` can be directly consumed by library `Bar`. If not being able to set global state it would take much effort for library authors to add additional input argument for virtually every Boost.Compute call.

---

## Comment 1

> Username: rosenrodt  
> Created at: 2019-04-01 02:25:59 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-478414307  

I have a [WIP patch](https://github.com/boostorg/compute/compare/master...rosenrodt:pr-set-default-context) that lets you set global context at the very first call to `system::default_context()`, via an additional input argument `user_context`. Afterwards the default queue will be derived from the user device (which is in turn derived from user context) and the provided user context itself. It does not change the old behavior if additional input argument is not provided.   
  
This gives the same guarantee that the global states is only set when `default_device/context/command_queue()` is called for the first time. Any subsequent call afterwards just returns the same value over again.   
  
It does not yet guarantee thread safety for the first call. They have to be done atomically or it will have UB when threads concurrently reading and writing the static variables at the same location. Maybe I could add a lock guard inside the calls but it may take a performance hit.

---

## Comment 2

> Username: jszuppe  
> Created at: 2019-04-01 12:10:59 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-478553476  

The idea overall is ok. However, I think having something like `set_default_context()` or `switch_default_context()` would be better, because you can return the old default context. Implementation may be less straighforward compared to your patch.   
  
>It does not yet guarantee thread safety for the first call. They have to be done atomically or it will have UB when threads concurrently reading and writing the static variables at the same location. Maybe I could add a lock guard inside the calls but it may take a performance hit.  
  
Right, I think that would be a good idea. We don't want to break someone.

---

## Comment 3

> Username: rosenrodt  
> Created at: 2019-04-02 14:58:14 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-479036396  

> The idea overall is ok. However, I think having something like set_default_context() or switch_default_context() would be better, because you can return the old default context. Implementation may be less straighforward compared to your patch.  
  
I think it's going to be very contrived. How could two functions, say `set_default_context(context)` and `default_context()` share the same scoped static variable `default_context`? We cannot use global static variable here due to Boost.Compute's header-only nature.  
  
Also, I am not sure of the benefit of switching the default context mid-run. Many of the Boost.Compute algorithms do not check if user-specified command queue matches input vector's context. Something like  
```c++  
using namespace compute;  
vector a(42); // allocated with default context  
set_default_context(user_ctx);  
vector b(42); // allocated with user context  
copy(a.begin(), a.end(), b.begin()); //  would crash! context mismatch  
```  
probably introduces more problems than we want. My proposed patch avoids this problem, by forcing the user to settle down default context at program start.  
  
If the original default context found by Boost.Compute is desired, maybe we can make `find_default_device()` a public method. Anyway, I enjoy putting this up for discussion since this patch takes away some of the succinctness of Boost.Compute and I am not really sure what's the best to do

---

## Comment 4

> Username: jszuppe  
> Created at: 2019-04-06 12:37:39 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-480500815  

> I think it's going to be very contrived. How could two functions, say set_default_context(context) and default_context() share the same scoped static variable default_context? We cannot use global static variable here due to Boost.Compute's header-only nature.  
  
You're already doing something similar in your PR by having `static` variable in private member functions of class `system`. See https://ideone.com/iZQwsb  
  
Your current approach in #831 has a few problems:  
  
* You create defaults using `new` operator, but you never free the memory.  
   * Static value instead of static pointer is safer.   
* You can have a mismatch between default device and default context.  
* If I, as a user, provide you an OpenCL context with multiple devices, you will select the first one as the default, which may not be user's choice (and as you've argued users want to use default context/device/queue instead of user-created context/device/queue).  
   * Maybe instead, we should only a function to the default command queue (or for queue and context)?   
   * Also, IMHO, API should prevent default device/context/queue mismatch.   
* For accessing default values multiple-readers, one writer synchronisation would be more efficient (this would require `shared_mutex` and `shared_lock` from C++14 or Boost).

---

## Comment 5

> Username: rosenrodt  
> Created at: 2019-04-07 16:18:06 UTC  
> Updated at: 2019-04-08 07:31:44 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-480605838  

@jszuppe Thanks for providing an example. I wasn't able to wrap my head around that but now I do.   
  
Allowing user to set default command queue instead of default context, as you suggested, is a great alternative. It conveniently avoids cases where the user-provided context associates with more than one device. I'll go through the API design choices we have so far (correct me if missing anything):  
  
1. We let user attach their own default command queue via `default_queue()`. It's associated device and context are automatically set by Boost.Compute as default device and default context, respectively. Default command queue and context are retained to prevent deleting associated resources. **<-- my bet is on this now**  
  
2. We let user attach their own default context via `default_context()`. As before, associated device and newly created command queue are set as default. But further implicitly restricting that the provided context be associated with one device only or it will throw an exception.  
  
3. We create a new API to let user attach context/device/queue all at once. And all of them are set as default.  
  
In all the above alternatives, API should detect default device/context/queue mismatch and they should be thread-safe.  
  
As for switching default context/queue/device during mid-run, I'm still not entirely convinced as it changes the behaviors of `default_context/device/queue()` significantly. All `default_xxx()`'s currently behave like when it's set, it's settled for the lifetime of a program or DLL. As well as implications of the resulting behavior change, as in the code snippet in my last comment. Perhaps you could provide a use case? If indeed practical, could it be done incrementally for the next possible PR or better do it all at once?  
  
As for the implementation details:  
> You create defaults using new operator, but you never free the memory.  
>   * Static value instead of static pointer is safer.  
  
The static instance is `new`ed only once for the lifetime of a program and deleted by the OS when program exits so it doesn't "leak" as in "leak while running". **EDIT: seem not to be the case here** ~In fact, with current static object, I observed static context object (instead of pointer) sometimes gets destroyed before `program_cache` does, so the time when `program`'s dtor are called the underlying `cl_program` are already gone with the `context` before it, throwing CL errors. Static pointer ensures context is released last.~ My rationale is based on [Why doesn’t the Construct On First Use Idiom use a static object instead of a static pointer](https://isocpp.org/wiki/faq/ctors#construct-on-first-use-v2)  
  
> For accessing default values multiple-readers, one writer synchronisation would be more efficient (this would require shared_mutex and shared_lock from C++14 or Boost).  
  
I can adapt to C++14 shared_mutex and shared_lock. However current implementation doesn't require writing the static instance more than once. In this case C++11 atomic is enough and supposedly more lightweight, because reader threads only need to perform one atomic load for all subsequent reads.

---

## Comment 6

> Username: jszuppe  
> Created at: 2019-04-07 18:58:30 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-480619360  

> In fact, with current static object, I observed static context object (instead of pointer) sometimes gets destroyed before program_cache does, so the time when program's dtor are called the underlying cl_program are already gone with the context before it, throwing CL errors.  
  
Can you provide an example? I think I can think of one, but if you already have one working it would be simpler.  
  
> The static instance is newed only once for the lifetime of a program and deleted by the OS when program exits so it doesn't "leak" as in "leak while running". In fact, with current static object, I observed static context object (instead of pointer) sometimes gets destroyed before program_cache does, so the time when program's dtor are called the underlying cl_program are already gone with the context before it, throwing CL errors. Static pointer ensures context is released last. My rationale is based on Why doesn’t the Construct On First Use Idiom use a static object instead of a static pointer  
  
You have to remember that `context` does not have an empty destructor. If it's never called then OpenCL context is never released. I can't say how every OpenCL platform is implemented, thus I can't say for sure system will clean everything every time.   
  
> I'll go through the API design choices we have so far (correct me if missing anything):  
  
I would go with API only for default queue (which also changes default context and device).  
  
> As for switching default context/queue/device during mid-run, I'm still not entirely convinced as it changes the behaviors of default_context/device/queue() significantly. All default_xxx()'s currently behave like when it's set, it's settled for the lifetime of a program or DLL. As well as implications of the resulting behavior change, as in the code snippet in my last comment  
  
Issues like the one you presented in the example already occur when for example: users create a vector in default context and they try to use it with their own (not default) queue. I guess, if anyone is using Boost.Compute for commercial/big/important project I'd recommend not using defaults at all (I expect them not to use them).   
  
I'm not sure if there are significant reasons for not allowing user to change defaults at any time. However, I guess, for now we can try giving them [users] feature to change defaults in the runtime (not just by env variables). If someone comes knocking on our doors to implement change-at-any-time, then we can revisit it.  
  
> I can adapt to C++14 shared_mutex and shared_lock. However current implementation doesn't require writing the static instance more than once. In this case C++11 atomic is enough and supposedly more lightweight, because reader threads only need to perform one atomic load for all subsequent reads.  
  
I will comment on that later this week.

---

## Comment 7

> Username: rosenrodt  
> Created at: 2019-04-08 07:30:06 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-480716864  

> Can you provide an example? I think I can think of one, but if you already have one working it would be simpler.  
  
I just tried, using static pointer to context does not solves this problem. Maybe I'll open another ticket if I can repro the issue with minimum example.  
  
So let's use static object (as before) instead and allow user to attach command queue. I'll hear how you weigh in on atomic and shared_mutex. Your advices are of big help. Many thanks

---

## Comment 8

> Username: jszuppe  
> Created at: 2019-04-08 07:51:49 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-480723665  

> I just tried, using static pointer to context does not solves this problem. Maybe I'll open another ticket if I can repro the issue with minimum example.  
  
I think I might have a solution for this problem, but it would be great to have a minimum example for reproduction.   
  
> So let's use static object (as before) instead and allow user to attach command queue. I'll hear how you weigh in on atomic and shared_mutex. Your advices are of big help. Many thanks  
  
:+1:

---

## Comment 9

> Username: jszuppe  
> Created at: 2019-04-13 12:37:09 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-482805494  

> So let's use static object (as before) instead and allow user to attach command queue. I'll hear how you weigh in on atomic and shared_mutex.  
  
You're right for a situation when API only allows user to set value of a default queue/context/device only once. However, let's consider to what happens if default context can't be set by a user, but default command queue can be set by user, however only once:  
  
```cpp  
vector<float> a(10); // default context and default device are created  
set_default_queue(user_queue);  
vector b(10, 1.0f); // allocated using context from default queue, because there is mismatch.  
copy(a.begin(), a.end(), b.begin()); //  error, a and b are in different contexts.  
```  
  
We either need a single initialisation: defaults must be initialised at the same time, so the 2nd line fails as default queue was already set, or user needs to be able to change defaults multiple times.

---

## Comment 10

> Username: Ulfgard  
> Created at: 2019-04-13 14:46:10 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-482815205  

There are also other use-cases like default-queues per thread (similar to cuda streams). I think the behaviour should be: setting a default device/context should be a one time action while setting a queue can be done multiple times, as long as the queue is for the same device/context  
________________________________________  
From: Jakub Szuppe [notifications@github.com]  
Sent: Saturday, April 13, 2019 2:37 PM  
To: boostorg/compute  
Cc: Subscribed  
Subject: Re: [boostorg/compute] Support user-defined default context/device (#827)  
  
So let's use static object (as before) instead and allow user to attach command queue. I'll hear how you weigh in on atomic and shared_mutex.  
  
You're right for a situation when API only allows user to set value of a default queue/context/device only once. However, let's consider to what happens if default context can't be set by a user, but default command queue can be set by user, however only once:  
  
vector<float> a(10); // default context and default device are created  
set_default_queue(user_queue);  
vector b(10, 1.0f); // allocated using context from default queue, because there is mismatch.  
copy(a.begin(), a.end(), b.begin()); //  error, a and b are in different contexts.  
  
We either need a single initialisation: defaults must be initialised at the same time, so the 2nd line fails as default queue was already set, or user needs to be able to change defaults multiple times.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/compute/issues/827#issuecomment-482805494>, or mute the thread<https://github.com/notifications/unsubscribe-auth/AOWTBoguIS2qA2chG0sjRkb1ZIDYECPjks5vgc94gaJpZM4cURBi>.

---

## Comment 11

> Username: rosenrodt  
> Created at: 2019-04-14 14:48:32 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-482991354  

> We either need a single initialisation: defaults must be initialised at the same time, so the 2nd line fails as default queue was already set, or user needs to be able to change defaults multiple times.  
  
That falls into the goal we've had now: user is allowed one-time command queue setup. If there's context/device/queue mismatch the errors will be emitted.   
  
I haven't had time in the past week but I expect to work on it again starting this week  
   
> There are also other use-cases like default-queues per thread (similar to cuda streams)  
  
I think that could be done by declaring default command queue thread-local. I could certainly try

---

## Comment 12

> Username: jszuppe  
> Created at: 2019-04-14 16:20:08 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-483009500  

>  I think the behaviour should be: setting a default device/context should be a one time action while setting a queue can be done multiple times, as long as the queue is for the same device/context  
  
I'm not sure if that's good for average user. For power-user sure, but power-users should already be using their own queues. I don't want users to confuse themselves after switching queues multiple times. Can you present a scenario in which changing default queues (withing the same context and device) is useful?  
  
> There are also other use-cases like default-queues per thread (similar to cuda streams).   
  
I can agree with a macro which changes behaviour to per-thread default, or have a separate function, or both (but this can be delegated to separate issue), .  
  
> That falls into the goal we've had now: user is allowed one-time command queue setup. If there's context/device/queue mismatch the errors will be emitted.  
  
Yes, but it means that you have to initialise all those values (device, context, and queue) at the same time the first time user asks for either default device, default context, or default queue:  
  
```cpp  
vector<float> a(10); // default device, context and queue are set  
set_default_queue(user_queue); // assert in debug version, exception :( or no-op in release  
```

---

## Comment 13

> Username: rosenrodt  
> Created at: 2019-04-21 12:57:31 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-485249527  

The PR #832 now ensures the following behaviors. Please review  
  
```c++  
vector<float> a(10); // default device, context and queue are set  
command_queue user_queue;  
system::default_queue(&user_queue); // throws; device/context/queue mismatch  
```  
  
```c++  
compute::system::default_queue(&queue);  
compute::system::default_queue(&queue); // assert in debug, no-op in release  
```  
  
As for per-thread default queue (mirroring CUDA's per-thread default stream), I think it can be easily achieved using `BOOST_COMPUTE_DETAIL_GLOBAL_STATIC` and additional config macro for switching legacy and per-thread queues. @Ulfgard feel like contributing to it?

---

## Comment 14

> Username: jszuppe  
> Created at: 2019-04-21 17:54:01 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-485270348  

Nice. What happens if someone asked for default device and then want to set default queue?

---

## Comment 15

> Username: rosenrodt  
> Created at: 2019-04-22 00:18:17 UTC  
> Url: https://github.com/boostorg/compute/issues/827#issuecomment-485292936  

The system would check if user-provided queue is consistent with default device & context. If not, then an exception will throw telling it's context/device/queue mismatch
