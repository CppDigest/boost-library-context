# #65 - `pmr::monotonic_buffer_resource::allocate()` can return a pointer to freed memory after `release()` is called [Closed]

> Username: nickhutchinson  
> Created at: 2017-12-20 21:34:31 UTC  
> Updated at: 2018-01-27 20:26:34 UTC  
> Closed at: 2018-01-27 20:26:34 UTC  
> Url: https://github.com/boostorg/container/issues/65  

As implemented, calling `release()` on a `pmr::monotonic_buffer_resource` leaves it in an inconsistent state, and this means calling `allocate()` can return a pointer to memory freed by the call to `release()`.  
  
The 1.65.1 implementation of `release()` only resets one of the member variables:  
```  
void monotonic_buffer_resource::release() BOOST_NOEXCEPT  
{  m_memory_blocks.release();  }  
```  
  
The member variables `m_current_buffer` and `m_current_buffer_size` (used in `do_allocate()`) are untouched.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-01-27 20:26:34 UTC  
> Url: https://github.com/boostorg/container/issues/65#issuecomment-361012808  

The commit leaves the monotonic buffer as default-constructed. Thanks for the report.
