# #49 - Create continuation without resuming [Closed]

> Username: viccpp  
> Created at: 2017-03-27 08:37:25 UTC  
> Updated at: 2017-03-28 06:31:25 UTC  
> Closed at: 2017-03-27 08:42:19 UTC  
> Url: https://github.com/boostorg/context/issues/49  

How to create new continuation but don't switch the context? `callcc()` always calls `resume()`. With old `execution_context` we could just use the constructor. Why such constructor was removed?  
It is useful when one creates a number of coroutines then starts all of them after successful creation.

---

## Comment 1

> Username: olk  
> Created at: 2017-03-27 08:42:19 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289390215  

Its the semantic of callcc() (for instance in Scheme or Ruby or ...) - name of callcc() means 'call-with current-continuation'. The function passed to callcc() is invoked while the continuation of the current execution context is passed as the first parameter.

---

## Comment 2

> Username: olk  
> Created at: 2017-03-27 08:45:27 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289390880  

A continuation is/represents the suspended state of a execution context. You can't get a valid continuation instance from a running/resumed or terminated execution context.

---

## Comment 3

> Username: viccpp  
> Created at: 2017-03-27 08:46:15 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289391055  

I don't ask changing `callcc()` behaviour. I need another way to create continuation without immediate context switch. Like for `execution_context`

---

## Comment 4

> Username: viccpp  
> Created at: 2017-03-27 08:49:20 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289391773  

>  You can't get a valid continuation instance from a running/resumed or terminated execution context.  
  
Why? It is exactly what `callcc()` does.  
  
```  
template< typename StackAlloc, typename Fn >  
continuation  
callcc( std::allocator_arg_t, preallocated palloc, StackAlloc salloc, Fn && fn) {  
    using Record = detail::record< continuation, StackAlloc, Fn >;  
    return continuation{  
                detail::context_create< Record >(  
                        palloc, salloc, std::forward< Fn >( fn) ) }.resume();  
}  
```  
  
continuation is created and it can be resumed

---

## Comment 5

> Username: viccpp  
> Created at: 2017-03-27 09:19:04 UTC  
> Updated at: 2017-03-27 09:33:48 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289398758  

> It is useful when one creates a number of coroutines then starts all of them after successful creation.  
  
Could you provide any solution using current library facilities?

---

## Comment 6

> Username: olk  
> Created at: 2017-03-27 10:34:32 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289416097  

You could still use execution_context from boost.context (but it's deprecated).  
boost.coroutine2 and boost.fiber use callcc() - for instance you could use the first context switch to install/prepare stuff.

---

## Comment 7

> Username: viccpp  
> Created at: 2017-03-27 12:13:31 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289435533  

> You could still use execution_context from boost.context (but it's deprecated).  
  
I would rather make my own `continuation` on top of `detail::jump_context()`. `execution_context<void>` has some additional overhead compared with continuation, it is deprecated (doublful choice for new code) and it anyway doesn't have function like `std::thread::detach()`:  
  
```c++  
void execution_context<void>::detach()  
{  
    fctx_ = {};  
}  
```  
  
I don't understand why `continuation` can't be constructed just like `execution_context<void>` in addition to `callcc()` but anyway, thank you very much for feedback and your time!

---

## Comment 8

> Username: olk  
> Created at: 2017-03-27 15:22:32 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289487528  

execution_context as well contiunation are thin wrappers over fcontext-API - both classes manage the associated stack + prevent fals usage (restarting a n already running context etc.).  
detach() does make sense at higher level abstractions - probably you have something like fibers/user-lands in mind. if this is the case you could take a look at boost.fiber (which provides a detach()).

---

## Comment 9

> Username: viccpp  
> Created at: 2017-03-28 06:21:34 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289674797  

fibers mimic threads interface. It isn't what I need. I have a network server which spawns new coroutine per incoming connection. Thereafter the coroutine controls its life time and self-destructs after disconnect. The main thread doesn't need the coroutine handler after spawn and initial context switch.

---

## Comment 10

> Username: viccpp  
> Created at: 2017-03-28 06:31:25 UTC  
> Url: https://github.com/boostorg/context/issues/49#issuecomment-289676433  

Have looked into boost.fiber. `context` is a huge object  
  
```c++  
    std::atomic< std::size_t >                      use_count_{ 0 };  
    std::atomic< unsigned int >                     flags_;  
    std::atomic< type >                             type_;  
    std::atomic< scheduler * >                      scheduler_{ nullptr };  
    boost::context::execution_context< detail::data_t * >   ctx_;  
    detail::ready_hook                      ready_hook_{};  
    detail::sleep_hook                      sleep_hook_{};  
    detail::terminated_hook                 terminated_hook_{};  
    detail::wait_hook                       wait_hook_{};  
    detail::worker_hook                     worker_hook_{};  
    std::atomic< context * >                remote_nxt_{ nullptr };  
    std::chrono::steady_clock::time_point   tp_{ (std::chrono::steady_clock::time_point::max)() };  
    fss_data_t                              fss_data_{};  
    wait_queue_t                            wait_queue_{};  
    detail::spinlock                        splk_{};  
    fiber_properties                    *   properties_{ nullptr };  
```  
It's overkill, I don't need all this stuff. All I need is a coroutine context + `std::exception_ptr` to transfer exceptions between sheduler and coroutine.
