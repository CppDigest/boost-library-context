# #349 - The correct parameter type to `set_receive_response` is neither documented nor enforced. [Closed]

> Username: rfmerrill  
> Created at: 2025-11-07 22:43:15 UTC  
> Updated at: 2026-02-23 14:59:43 UTC  
> Closed at: 2026-02-23 14:59:43 UTC  
> Url: https://github.com/boostorg/redis/issues/349  

currently `connection::set_receive_response` can't fail, and it will accept a reference to any `Response` object.  
  
If that `Response` object can't accommodate what we get from the server, it cancels the channel and `async_receive` returns cancelled. This took me a couple hours to figure out in a coworker's code just now.  
  
I'm having a hard time using either the Redis documentation or the Boost.Redis documentation figuring out if it is _ever_ ok to use a fixed response type here and if so what that fixed response type should be.  
  
If a fixed response is not allowed, this should at least be documented in the `set_receive_response` page in the reference, and ideally the method should be made to only accept a reference to a generic response object.  
  
If a fixed response _is_ allowed, the type(s) of fixed response(s) you can use should be documented somewhere and/or shown in example code. And, if possible, the method should be made to only accept valid ones (although this seems likely tricky).

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-11-08 10:33:51 UTC  
> Url: https://github.com/boostorg/redis/issues/349#issuecomment-3506418603  

> If a fixed response is not allowed, this should at least be documented in the set_receive_response page in the reference, and ideally the method should be made to only accept a reference to a generic response object.  
  
I agree with you here. The response set in `set_receive_response` must be able to accomodate an unspecified number of pushes, at the moment this number is 256 but we don't commit to that.  
  
@anarthal I don't think will want to pass their own response types to `set_receive_response`. We can safely remove the template and use `generic_response`. When our work on `generic_flat_response` is finally merged we can add a new overload. For full flexibility we can also add an overload that takes an `any_adapter` like we did for `async_exec`, then all use cases will be covered.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-11-12 16:23:44 UTC  
> Url: https://github.com/boostorg/redis/issues/349#issuecomment-3522779740  

Sorry for the delay. Having an overload taking an `any_adapter` is equivalent to what we have now with the template.  
  
Since most use cases involve PubSub, I wonder if having a dedicate response type for it would be worth it. Using `resp3::node` is not trivial. `consume_one` makes this less problematic ATM, but it's inefficient and it's in the process of being deprecated.  
  
PubSub pushes always have type push, and a fixed structure. In the simplest case, we could support something like:  
  
```cpp  
struct pubsub_message {  
    std::string channel_name;  
    std::string message;  
};  
  
// We add support in adapters for vector<pubsub_message>  
std::vector<pubsub_message> resp;  
conn.set_receive_response(resp);  
```  
  
This can likely cover 95% of the use cases. For these that don't (e.g. `MONITOR`, extensions we even don't know about), use `any_adapter`.  
  
Some considerations  
* This response type would discard the initial "subscribe" and "unsubscribe" push messages that are received when you first subscribe/unsubscribe to a channel. (It would also discard any other push type).  
* We can make `pubsub_message` templated on the message type and use `boost_redis_from_bulk` to avoid temporaries (I suspect many people use JSON here).  
* We could create something like your `generic_flat_response`, but for pushes.
