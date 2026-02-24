# #458 - Document that JSON's default resource is always new_delete_resource [Closed]

> Username: vinniefalco  
> Created at: 2020-10-24 23:40:37 UTC  
> Updated at: 2022-05-24 07:39:13 UTC  
> Closed at: 2022-05-24 07:39:13 UTC  
> Url: https://github.com/boostorg/json/issues/458  

The allocators Background needs to compare and contrast std's `get_default_resource` which accesses a global, mutable state with Boost.JSON's approach of making the default resource always a `new_delete_resource`. At the same time we can change these lines:  
  
```  
// This vector will use the default memory resource  
std::vector< value, polymorphic_allocator < value > > v;  
```  
  
to no longer indirectly call `get_default_resource` (which causes a link error)

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-05-23 18:24:59 UTC  
> Url: https://github.com/boostorg/json/issues/458#issuecomment-1134999627  

[This](https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/allocators/storage_ptr.html) mentions that by default `storage_ptr` references a memory resource that is equivalent to using new and delete. Do we need something more elaborate or the issue could be closed?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-05-24 00:44:03 UTC  
> Url: https://github.com/boostorg/json/issues/458#issuecomment-1135277291  

Its good enough
