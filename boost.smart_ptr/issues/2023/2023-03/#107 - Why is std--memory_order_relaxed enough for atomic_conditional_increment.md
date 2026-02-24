# #107 - Why is std::memory_order_relaxed enough for atomic_conditional_increment? [Open]

> Username: kakashidinho  
> Created at: 2023-03-11 17:20:29 UTC  
> Updated at: 2023-03-12 00:19:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/107  

Hi, I'm just trying to understand the rationale behind the using of `std::memory_order_relaxed` in sp_counted_base_std_atomic.hpp's `atomic_conditional_increment`'s implementation.  
<details>  
  <summary>atomic_conditional_increment's implementation (click to expand)</summary>  
  
```  
inline std::int_least32_t atomic_conditional_increment( std::atomic_int_least32_t * pw ) BOOST_SP_NOEXCEPT  
{  
    // long r = *pw;  
    // if( r != 0 ) ++*pw;  
    // return r;  
  
    std::int_least32_t r = pw->load( std::memory_order_relaxed );  
  
    for( ;; )  
    {  
        if( r == 0 )  
        {  
            return r;  
        }  
  
        if( pw->compare_exchange_weak( r, r + 1, std::memory_order_relaxed, std::memory_order_relaxed ) )  
        {  
            return r;  
        }  
    }      
}  
```  
</details>  
  
AFAIK this function is used by `weak_ptr` to obtain the `shared_ptr` if the obj is still alive (ref count is still > 0).  
Consider the following case:  
```  
shared_ptr A;  
if ((A = weak_ptr.lock()) != nullptr) // internally does `if (atomic_conditional_increment() != 0)`  
{  
   // modify object pointed by A  
}  
```  
  
Shouldn't we use `std::memory_order_acquire` to ensure that the modification of object pointed by A won't be reordered before the `nullptr` check?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-03-11 17:40:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/107#issuecomment-1464960834  

The nullptr check and the modification would be on the same side of the acquire anyway, so they can still be "reordered".  
  
"Reordered" is not the right way to think about this anyway, because operations are never observably reordered within the same thread. In order for "reordering" to be observed, there needs to be another thread that can perceive events in a different order.  
  
Try adding another thread to the example and see if you can demonstrate a problem.

---

## Comment 2

> Username: kakashidinho  
> Created at: 2023-03-11 17:57:10 UTC  
> Updated at: 2023-03-11 17:59:23 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/107#issuecomment-1464965186  

Honestly I'm not an expert in C++ memory order. So I'm using this question in hope it could help me understand a bit better.  
  
I meant the modification could be reordered to before the ref count is increased successfully/unsuccessfully by `compare_exchange_weak`.  
What if at the same time other thread does the decrement of ref count to zero, and deletes the object? Could it be possible to happen?  
Though this most likely leads to a wrong prediction, and the processor could throw away the modification's results on the 1st thread anw. If I understand correctly how processor's branch prediction works.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-03-12 00:19:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/107#issuecomment-1465056447  

> What if at the same time other thread does the decrement of ref count to zero, and deletes the object? Could it be possible to happen?  
  
No, because if this happens first, the atomic_conditional_increment will fail, and if it happens second, the reference count will not be zero so the object won't be destroyed.
