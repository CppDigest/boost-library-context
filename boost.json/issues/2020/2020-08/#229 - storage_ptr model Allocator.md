# #229 - storage_ptr model Allocator? [Closed]

> Username: vinniefalco  
> Created at: 2020-08-29 14:50:16 UTC  
> Updated at: 2020-09-01 14:56:09 UTC  
> Closed at: 2020-09-01 14:56:09 UTC  
> Url: https://github.com/boostorg/json/issues/229  

Can `storage_ptr` be improved so it can be used where _Allocator_ is expected?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-01 14:56:09 UTC  
> Url: https://github.com/boostorg/json/issues/229#issuecomment-684915717  

Nope, because `rebind` is unimplementable (as `storage_ptr` is not a template). We could however add a wrapper that is an allocator which uses storage_ptr under the hood, see #238
