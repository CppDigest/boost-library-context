# #16 - Error: deque iterators incompatible [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-25 11:22:54 UTC  
> Updated at: 2022-07-25 12:31:32 UTC  
> Closed at: 2022-07-25 12:31:32 UTC  
> Url: https://github.com/boostorg/redis/issues/16  

Aedis raised error due to incompatible iterators in function  void cancel_push_requests(typename reqs_type::iterator end), called from writer_op as conn->cancel_push_requests(end).    
  
Failed on:  
auto point = std::stable_partition(std::begin(reqs_), end, [](auto const& ptr) {  
         return ptr->req->commands() != 0;  
      });

---

## Comment 1

> Username: Eddie-cz  
> Created at: 2022-07-25 12:31:32 UTC  
> Url: https://github.com/boostorg/redis/issues/16#issuecomment-1193989201  

Problem in 0.2.0, solved in 0.2.1, closing.
