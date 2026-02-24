# #27 Fix undefined behavior [Merged]

> Username: stheophil  
> Created at: 2016-06-28 08:42:21 UTC  
> Updated at: 2016-06-29 12:44:38 UTC  
> Merged at: 2016-06-29 12:44:24 UTC  
> Closed at: 2016-06-29 12:44:24 UTC  
> Url: https://github.com/boostorg/interprocess/pull/27  

`managed_open_or_create_impl::priv_open_or_create` calls  
`construct_func.get_min_size()` before `construct_func::operator()`.  
  
The implementation of `create_open_func::get_min_size` called the static  
function `segment_manager::get_min_size()` by making a member function  
call on `m_frontend->get_segment_manager()` which is still null at this  
point.  
  
This is undefined behavior and crashes when compiling with clang’s undefined behavior sanitizer.  
  
The call to `construct_func.get_min_size()` remains a member-function call, because `msg_queue_initialization_func_t::get_min_size` must be a member function.   
  
I ran all interprocess tests and they passed.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2016-06-29 12:44:38 UTC  
> Url: https://github.com/boostorg/interprocess/pull/27#issuecomment-229344645  

Thanks for the patch

---
