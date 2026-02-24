# #256 - Using concurrent_flat_map with interprocess mapped memory allocator [Closed]

> Username: bocharov  
> Created at: 2024-06-18 01:47:54 UTC  
> Updated at: 2025-02-21 03:08:23 UTC  
> Closed at: 2024-07-10 14:42:45 UTC  
> Url: https://github.com/boostorg/unordered/issues/256  

Hello,  
  
I have a use case that involves using Boost's concurrent_flat_map with an interprocess mapped memory allocator. My application is designed to be read-heavy with occasional inserts and deletes. It also requires multiple processes (not just threads) to access the container concurrently.  
  
I have two questions:  
  
1. Is it possible to use concurrent_flat_map with an interprocess mapped memory allocator in such a way that multiple processes can access and modify the map concurrently?  
2. If the above is not feasible, could you recommend an alternative container or approach within Boost that would suit this use case? My primary goals are to minimize synchronization costs and ensure efficient read access across multiple processes.  
  
Thank you for your assistance!

---

## Comment 1

> Username: joaquintides  
> Created at: 2024-06-22 07:17:04 UTC  
> Url: https://github.com/boostorg/unordered/issues/256#issuecomment-2183892632  

Yes, it works, see this example:  
  
https://github.com/boostorg/unordered/blob/develop/test/cfoa/interprocess_concurrency_tests.cpp  
  
Note, however, that this feature is not documented, so you would be using it unofficially. We haven't decided yet whether to guarantee it for future releases of the library.

---

## Comment 2

> Username: Feng-Zihao  
> Created at: 2025-02-21 03:08:22 UTC  
> Url: https://github.com/boostorg/unordered/issues/256#issuecomment-2673256940  

Hello, @joaquintides   
  
I think this feature is super great!  Strongly recommended to guarantee it for future release. At least for concurrent_flat_map.
