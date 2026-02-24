# #307 - pilfer storage_ptr [Closed]

> Username: vinniefalco  
> Created at: 2020-09-09 19:28:27 UTC  
> Updated at: 2020-09-26 03:07:41 UTC  
> Closed at: 2020-09-26 03:07:41 UTC  
> Url: https://github.com/boostorg/json/issues/307  

We have many places that use `std::move(sp)` to take ownership of the storage pointer, but then do nothing with the moved-from value. These call sites should use `pilfer`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-26 03:07:41 UTC  
> Url: https://github.com/boostorg/json/issues/307#issuecomment-699286059  

This is not necessary if the default memory resource is represented with `storage_ptr::i_ == 0`, as it is currently (and benchmarks show this is the best implementation choice)
