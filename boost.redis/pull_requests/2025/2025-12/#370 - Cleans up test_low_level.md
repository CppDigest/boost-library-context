# #370 Cleans up test_low_level [Open]

> Username: anarthal  
> Created at: 2025-12-06 11:16:54 UTC  
> Updated at: 2026-02-17 14:00:31 UTC  
> Url: https://github.com/boostorg/redis/pull/370  



---

## Comment 1

> Username: anarthal  
> Created_at: 2025-12-06 11:21:05 UTC  
> Url: https://github.com/boostorg/redis/pull/370#issuecomment-3619957261  

This is still in-progress, but the idea is cleaning up test_low_level a little bit and increasing the coverage we have for parsers and adapters. My idea is splitting test_low_level into  
  
* Parser tests. Given a valid input string, check that the correct sequence of output nodes is produced. No adapters involved.  
* Parser error tests. Given an invalid input string, check that the correct error code is produced. No adapters involved.  
* Single-field adapter tests. Given a valid sequence of nodes, check that the correct C++ value is generated.  
* Single-field adapter error tests. Given a sequence of nodes that is valid RESP3 but is incompatible with the adapter (e.g. we expect an int and got an array), check that the adequate error code is obtained.

---
