# #152 - Enable reading server pushes in batches [Closed]

> Username: mzimbres  
> Created at: 2023-09-04 11:43:26 UTC  
> Updated at: 2023-09-10 20:28:29 UTC  
> Closed at: 2023-09-10 20:28:29 UTC  
> Url: https://github.com/boostorg/redis/issues/152  

At the moment the `asio::channel` used to communicate server pushes to the user via `async_receive` has size 0, which is inefficient under heavy load. Given that the `generic_response` can contain multiple responses and pushes, there would be no problem is making the channel size configurable by the user.  
  
The implementation should also use the `try_send/receive` functions to avoid suspension when possible.  
  
It should be also documented that a `generic_response` might contain multiple server pushes for channels with `size != 0` and how to iterate over them, which at the moment must be done by checking nodes with depth 0. In the future we will add a nicer way of iterating over individual messages in a response.
