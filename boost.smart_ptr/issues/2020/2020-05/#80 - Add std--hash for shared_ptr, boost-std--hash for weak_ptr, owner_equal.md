# #80 - Add std::hash for shared_ptr, boost/std::hash for weak_ptr, owner_equal [Closed]

> Username: pdimov  
> Created at: 2020-05-27 15:23:14 UTC  
> Updated at: 2020-06-06 07:21:44 UTC  
> Closed at: 2020-06-06 07:21:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/80  

(Or specialize std::equal_to instead of having to use owner_equal?)

---

## Comment 1

> Username: pdimov  
> Created at: 2020-05-31 19:16:59 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/80#issuecomment-636515499  

owner_hash too? As shared_ptr's hash uses the pointer, not the control block.

---

## Comment 2

> Username: pdimov  
> Created at: 2020-06-04 17:59:56 UTC  
> Updated at: 2020-06-06 07:21:35 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/80#issuecomment-639013118  

- [x] std::hash for shared_ptr, intrusive_ptr, local_shared_ptr  
- [x] hash_value, std::hash, std::equal_to for weak_ptr  
- [x] .owner_equals  
- [x] owner_equal_to  
- [x] .owner_hash_value  
- [x] owner_hash  
- [x] test/sp_unordered_test
