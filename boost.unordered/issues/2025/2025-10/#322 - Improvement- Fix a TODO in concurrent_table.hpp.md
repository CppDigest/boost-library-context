# #322 - Improvement: Fix a TODO in concurrent_table.hpp [Open]

> Username: Reneg973  
> Created at: 2025-10-13 15:12:24 UTC  
> Updated at: 2025-10-13 15:12:24 UTC  
> Url: https://github.com/boostorg/unordered/issues/322  

I tried to replace `std::unordered_map` with `boost::concurrent_flat_map` and saw a memory footprint I could not explain. It was easy to find this line:  
  `using multimutex_type=multimutex<mutex_type,128>; // TODO: adapt 128 to the machine`  
  
Can this be fixed?
