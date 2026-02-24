# #184 - Truncating implicit conversion warning in `object_impl::buckets` for 32 bit [Closed]

> Username: sdkrystian  
> Created at: 2020-08-22 14:00:30 UTC  
> Updated at: 2020-08-24 19:15:39 UTC  
> Closed at: 2020-08-24 19:15:39 UTC  
> Url: https://github.com/boostorg/json/issues/184  

We need to figure out what the correct solution here is. Options are to simply use `uint64_t` as the return type of `buckets`, or to entirely remove `max_load_factor` which will make the `buckets` function not needed.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-08-24 19:15:39 UTC  
> Url: https://github.com/boostorg/json/issues/184#issuecomment-679314771  

Fixed by 19c83c2af51d4ec7675c33c13d3a672bbee77a87
