# #9 - Interoperability 32/64 sizeof and hashing [Closed]

> Username: CerosDev  
> Created at: 2018-11-12 16:15:24 UTC  
> Updated at: 2018-11-29 16:06:43 UTC  
> Closed at: 2018-11-29 16:06:43 UTC  
> Url: https://github.com/boostorg/unordered/issues/9  

**Situation**  
We are storing a `unordered_map`, `unordered_multi_map` and `unordered_set` in a Boost Interprocess Shared Memory that is accessed by a 32bit process and a 64bit process. The 32bit process or the 64bit process can be the creator of the object.  
  
**Symptoms**  
If a 64bit process creates these containers, the 32bit process will crash trying to retrieve it and vice-versa.  
If a 64bit process inserts object into one of the containers, the 32bit process will fail to retrieve them by hashing and vice-versa.  
  
**Solution**  
I had to modify some headers so that the sizeof of the unordered's implementation is exactly the same in 32 and 64bits.   
In my own code, I force the hashing to be on 32bit instead of `size_t`. There is some issues internally around the policy. It will fall on a different logic and yield different result if on 32bit or 64bit.  
  
See patch attached.  
[boost-1.68.0-fix-interoperability-32-64-unordered.patch.zip](https://github.com/boostorg/unordered/files/2572645/boost-1.68.0-fix-interoperability-32-64-unordered.patch.zip)  
  
See related issue for Boost Multi-Index https://github.com/boostorg/multi_index/issues/17

---

## Comment 1

> Username: CerosDev  
> Created at: 2018-11-26 16:13:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/9#issuecomment-441697897  

Would it be possible to use the allocator's size_type to change the type of `bucket_count_`, `size_` and `max_load_` and use the allocator's size_type to determine the policy to use?
