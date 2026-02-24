# #129 - Fix the generic_response adapter [Closed]

> Username: mzimbres  
> Created at: 2023-07-30 06:05:44 UTC  
> Updated at: 2025-11-18 13:44:56 UTC  
> Closed at: 2025-11-18 13:44:56 UTC  
> Url: https://github.com/boostorg/redis/issues/129  

The `generic_response` adapter should not set an error in the `result` but let the caller check in each `node` whether an error occurred. Otherwise we cannot process responses to more than one request properly, like in the case of transactions.  
  
The current implementation also has bug now since it might call `result_->value()` after an error has beed set in https://github.com/boostorg/redis/blob/46525371b9247a1ec208a6157880ec77f5ddadc4/include/boost/redis/adapter/detail/adapters.hpp#L101

---

## Comment 1

> Username: anarthal  
> Created at: 2025-11-06 15:52:01 UTC  
> Url: https://github.com/boostorg/redis/issues/129#issuecomment-3497973066  

I've stumbled upon this in the Sentinel PR. I think we need a way to solve this.  
  
Since `generic_response` is an alias for `result<vector<node>>`, the way this is implemented makes sense to me. What would you think of adding an adapter for `vector<node>` that implements this feature? This way we don't break existing code. It sort of makes sense:  
  
* I provided `result<X>` => I want error checking done for me  
* I provided X => I will do the error checking myself  
  
@mzimbres what do you think?

---

## Comment 2

> Username: mzimbres  
> Created at: 2025-11-18 13:44:12 UTC  
> Url: https://github.com/boostorg/redis/issues/129#issuecomment-3547712083  

@anarthal I think this is a good idea. I have implemented this for both the `generic_response` and the `generic_flat_response`, see this for example https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/test/test_conn_echo_stress.cpp#L119-L120  
  
- `result<flat_tree>` https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/adapter/detail/adapters.hpp#L228-L246  
  
- `flat_tree` https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/adapter/detail/adapters.hpp#L264-L270  
  
- `result<tree>` https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/adapter/detail/adapters.hpp#L156-L178  
  
- `tree` https://github.com/boostorg/redis/blob/00f3ec9b78454211c7b8d461b11411c8b1ef1ad3/include/boost/redis/adapter/detail/adapters.hpp#L197-L205  
  
The pubsub examples are still using `result<X>` but I am wondering whether they should use `X` instead. In any case I think we can close this issue.
