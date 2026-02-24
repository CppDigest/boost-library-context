# #61 Adding ptr compression support to MSVC ARM64 architecture [Closed]

> Username: mishatal  
> Created at: 2020-06-22 20:28:56 UTC  
> Updated at: 2020-06-23 18:13:25 UTC  
> Closed at: 2020-06-23 18:13:25 UTC  
> Url: https://github.com/boostorg/lockfree/pull/61  

Support of pointer compression has already been added to gcc (__ aarch64 __ define). MSVC compiler doesn't provide the __ aarch64 __ define on ARM64 architecture. Instead of that it provides _M_ARM64.  
  
The reasoning to add pointer compression support is for the lockfree queue is when the element type of the queue is pointer type. Currently when the queue is using tag_ptr wrapper for the queue node the tag_ptr wrapper doubles the size of the pointer and thus exchange operations  cannot be atomic (std::atomic::is_lock_free returns false). That makes lockfree queue is not lockfree (queue::is_lock_free returns false).  
By setting BOOST_LOCKFREE_PTR_COMPRESSION 1 define we tell the compiler to include tagged_ptr_ptrcompression.hpp file instead of tagged_ptr_dcas.hpp. tagged_ptr_ptrcompression.hpp keeps the pointer size intact by using bits manipulation to keep tag field within 64bits.  
  
The versification is done on the 16.4 MSVC compiler and Amberwing ARM64 CPU platform.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2020-06-23 03:13:48 UTC  
> Url: https://github.com/boostorg/lockfree/pull/61#issuecomment-647881641  

i'd prefer to use boost.predef. could you potentially verify #62? thanks a lot!

---

## Comment 2

> Username: mishatal  
> Created_at: 2020-06-23 18:13:25 UTC  
> Url: https://github.com/boostorg/lockfree/pull/61#issuecomment-648331137  

We have decided to use boost.predef to detect armv8. See #62 for final solution.

---
