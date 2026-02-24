# #183 - Off-by-one error in container_device example? [Open]

> Username: pwiecz  
> Created at: 2025-02-17 08:03:45 UTC  
> Updated at: 2025-02-17 08:04:14 UTC  
> Url: https://github.com/boostorg/iostreams/issues/183  

I suspect that line https://github.com/boostorg/iostreams/blob/develop/example/container_device.hpp#L135 should not contain the "- 1"  
When I compare behaviour of stream based on `container_device<std::string>` with `std::stringstream` we can see that they show different behaviours:  
https://godbolt.org/z/cfq5fM6M6
