# #350 - Allocate only once for the request notifier and multiplexer::elem [Open]

> Username: mzimbres  
> Created at: 2025-11-19 10:00:18 UTC  
> Updated at: 2025-11-23 20:48:06 UTC  
> Url: https://github.com/boostorg/redis/issues/350  

We currently perform one dynamic allocation for the `notifier` and one for the `multiplexer::elem` https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/connection.hpp#L193-L194  
  
It looks like it is possible to put these two data structures into a single `struct` and allocate only once. As part of the ticket we should also cache these structs to achieve asymptotically zero allocations.  
  
PS1: Do we need to allocate `multiplexer::elem` dynamically at all? These structs are not that big and it looks like they can be also copied. Btw, are there any lower overhead alternatives to `shared_ptr` for single-threaded use?  
  
PS2: Is there any alternative to `std::function` with better small buffer optimization? Most adapters are pretty small data structures.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-11-19 17:25:40 UTC  
> Url: https://github.com/boostorg/redis/issues/350#issuecomment-3553871127  

If I were writing this (I can write it, but there are things in the queue ATM), I'd create a type bundling what `multiplexer::elem` has today, the channel and the output function. Something like this:  
  
```cpp  
// This is (almost) the equivalent of today's element, but has no done function  
struct elem_data {  
      request const* req_;  
      any_adapter adapter_;  
      std::size_t remaining_responses_;  
      status status_;  
      system::error_code ec_;  
      std::size_t read_size_;  
};  
  
// Abstract base class for multiplexer elements.  
// The multiplexer does everything through this type.  
class multiplexer_elem {  
public:  
    elem_data data; // what we have today, sans the done function  
    virtual ~multiplexer_elem() {}  
    virtual void on_done(error_code) = 0;  
};  
  
// Concrete type. Created by connection::async_exec  
template <class Executor>  
class multiplexer_elem_impl final {  
    channel<void(error_code)> chan_; // has the channel  
public:  
    elem_data data; // what we have today, sans the done function  
    void on_done(error_code) override { chan_.try_send( /* ... */ ); }  
};  
```  
  
In the following iteration, you might remove the `any_adapter` member in `elem_data` and add a 2nd template parameter to `multiplexer_elem_impl` with the concrete adapter.  
  
In the following, you can cache these `multiplexer_elem_impl` objects in `connection_impl`.  
  
And finally, you might consider using a linked list instead of a `std::deque` to avoid all allocations altogether.  
  
I'd do none of this without having a metric to check against, though.

---

## Comment 2

> Username: mzimbres  
> Created at: 2025-11-23 20:48:06 UTC  
> Url: https://github.com/boostorg/redis/issues/350#issuecomment-3568322029  

> I'd do none of this without having a metric to check against, though.  
  
I see the proposed change as a general quality improvement and not exactly as performance optimization. Dynamic allocations in long running process leads to performance degradation over time. Reusing the elements would make it more reliable, better yet to have the ability to reserve elements.  
  
I agree however that a metric helps understanding the impact. What I use regularly is  
  
1. Client and server CPU usage. A good client should drive server CPU as high as possible.  
2. It should be possible for the client to saturate the CPU if it is not IO bound.  
3. The more time the client spends executing in the system (as opposed to user space) the better.  
  
For example, I have made changes to `test_conn_echo_stress` to use a unix socket so that the setup does not get IO bound. I see the following for the enumeration above  
  
1. 66% vs 100%: That is surprising because the client has more work to do that the server. I am not sure why this happens but it is a good sign.  
2. Given the server is saturating before the client there is no concern here as we won't be able to saturate the client CPU.  
3. The split is 13% sys and 87% user. This is more or less expected, a unix socket is fast an so the client won't seat too long idle waiting for IO.  
  
I believe if we implement this there will be a visible change in these numbers.
