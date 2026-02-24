# #20 Extend message_queue to support lazy initialization [Closed]

> Username: Lingxi-Li  
> Created at: 2015-10-13 17:43:21 UTC  
> Updated at: 2015-11-23 03:35:30 UTC  
> Closed at: 2015-11-23 03:35:30 UTC  
> Url: https://github.com/boostorg/interprocess/pull/20  

Both `shared_memory_object` and `mapped_region` support lazy initialization, so I think `message_queue` should support it also. Specifically, the following methods are added to `message_queue_t`:  
- Default constructor  
- Move constructor/assignment operator  
- `swap()`  
- `is_open()` (the naming convention follows `std::basic_filebuf::is_open()`)  
  
I noticed that `managed_open_or_create_impl::get_user_address()` cannot be properly used to check initialized state, for it may lead to segmentation fault when in an uninitialized state. So, another method is added to `managed_open_or_create_impl` to do the job:  
- `is_inited()`  
  
Using `send`, `receive` and their variants on an uninitialized message queue is a logic error. To indicate that, the following enumerator is added to the `error_code_t` enum:  
- `not_inited_error`  
  
Finally, only methods (no attributes) are added to the two class templates, and the addition does not break existing code. Besides, comments are added properly to support Doxygen.

---
