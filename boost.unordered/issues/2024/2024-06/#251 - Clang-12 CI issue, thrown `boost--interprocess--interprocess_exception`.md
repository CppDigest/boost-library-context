# #251 - Clang-12 CI issue, thrown `boost::interprocess::interprocess_exception` [Closed]

> Username: k3DW  
> Created at: 2024-06-06 01:13:45 UTC  
> Updated at: 2024-06-08 09:17:43 UTC  
> Closed at: 2024-06-08 09:17:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/251  

See [this job](https://github.com/boostorg/unordered/actions/runs/9339775687/job/25704579415) for example.  
  
This happens in these tests  
- foa_mmap_concurrent_flat_map_tests  
- foa_mmap_concurrent_flat_set_tests  
- foa_mmap_unordered_multimap_tests  
- foa_mmap_unordered_multiset_tests  
  
This happens specifically on the Ubuntu 20.04 container with clang-12. It only happens with *both* `address-sanitizer=norecover` and `undefined-sanitizer=norecover`.
