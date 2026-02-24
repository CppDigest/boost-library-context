# #366 Adds pubsub_message [Closed]

> Username: anarthal  
> Created at: 2025-12-02 10:24:36 UTC  
> Updated at: 2026-01-27 17:19:28 UTC  
> Closed at: 2026-01-27 17:19:28 UTC  
> Url: https://github.com/boostorg/redis/pull/366  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-12-02 10:44:35 UTC  
> Url: https://github.com/boostorg/redis/pull/366#issuecomment-3601385076  

@mzimbres this is an attempt to make it easier to work with PubSub, in line with what the user in #349 reported. The idea is to help users that only use PUBLISH and SUBSCRIBE (vs. advanced things like MONITOR) with a higher level response type. By using `generic_flat_response`, you can currently receive:  
  
* Pushes with the 1st element being `"subscribe"` and `"unsubscribe"`, which need to be discarded.  
* Pushes with the 1st element being `"message"`. The user needs to extract the 2nd and 3rd element here, which are the channel and the payload.  
* Other types of messages, which can result from the edge cases we have in multiplexer. These would need to be discarded.  
  
What I'm proposing here is a response type that does this message interpretation for the user. ATM it only supports PUBLISH/SUBSCRIBE, but I can extend it to support PSUBSCRIBE, too.  
  
I've made the payload templated so users can use their own types. Since Publish only supports a single string argument, I suspect there will be people putting serialized data into it.  
  
The only thing I don't like about this approach is that it doesn't have amortized 0 allocations as `generic_flat_response` has. An alternative design could be:  
  
```cpp  
  
// Like in the PR, but they're views, and the payload is always a string  
struct pubsub_message {  
    std::string_view channel_name;  
    std::string_view payload;  
};  
  
class pubsub_response {  
    // Implementation similar to generic_flat_response  
public:  
    // pubsub_response is a container of pubsub_message elements  
    span<const pubsub_message> get_messages() const;  
};  
```  
  
This gives us amortized 0 allocations, but prevents in-place deserialization (it's also more complex).  
  
What do you prefer?  
1. Templated pubsub_message  
2. Flat pubsub_response  
3. Do nothing, stay with generic_flat_response and let the user parse the nodes themselves, maybe add docs.

---
