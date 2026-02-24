# #300 - Warnings when building with clang-20 [Closed]

> Username: greg7mdp  
> Created at: 2025-04-17 19:48:31 UTC  
> Updated at: 2025-09-06 10:27:19 UTC  
> Closed at: 2025-09-06 10:27:19 UTC  
> Url: https://github.com/boostorg/container/issues/300  

Here are the warnings:  
  
```  
boost/libs/container/include/boost/container/detail/copy_move_algo.hpp:231:20: warning: first argument in call to 'memmove' is a pointer to non-trivially copyable type 'value_type' (aka 'std::pair<fc::sha256, bool>') [-Wnontrivial-memcall]  
```  
  
which are caused by this code:  
  
https://github.com/boostorg/container/blob/866fd30df1ab723e2104e3de504f4a8c116f2c41/include/boost/container/detail/copy_move_algo.hpp#L219-L235  
  
Where you can see that the destination of the `std::memmove`, `dest_raw`, is of type: `value_type *const`.  
  
So clang checks that the type `value_type` is `trivially_copyable`. When the case is a `flat_map` for example, the `value_type` is a `std::pair`, which is *not* trivially_copyable regardless of the types it contains.  
  
So why is this library using `std::memmove` on a type that is not `trivially_copyable`. I think because the check here:  
  
https://github.com/boostorg/container/blob/866fd30df1ab723e2104e3de504f4a8c116f2c41/include/boost/container/detail/copy_move_algo.hpp#L371-L377  
  
is using :  
  
https://github.com/boostorg/container/blob/866fd30df1ab723e2104e3de504f4a8c116f2c41/include/boost/container/detail/copy_move_algo.hpp#L151-L157  
  
doesn't actually check that the type `I` is `trivially_copyable`  
  
It checks this:  
  
https://github.com/boostorg/move/blob/e9ff3ca0952e680871145f454925614d950cef4d/include/boost/move/detail/type_traits.hpp#L150-L154  
  
(see   
  
https://github.com/boostorg/move/blob/e9ff3ca0952e680871145f454925614d950cef4d/include/boost/move/detail/type_traits.hpp#L1045-L1049  
  
which uses:  
  
https://github.com/boostorg/move/blob/e9ff3ca0952e680871145f454925614d950cef4d/include/boost/move/detail/type_traits.hpp#L406-L412  
)  
  
If the check from `uninitialized_move_alloc` is sufficient for the use of `std::memmove` made in the container library (which I hope it is), I suggest to use a `void *const dest_raw` type to avoid the clang warning.

---

## Comment 1

> Username: nigels-com  
> Created at: 2025-09-05 02:04:19 UTC  
> Url: https://github.com/boostorg/container/issues/300#issuecomment-3256826220  

Confirming this for Ubuntu 24.04 (Noble) with clang 20.1.8  
No warnings for clang 18 or 19.  
Warnings for clang 21 also.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-09-06 10:27:19 UTC  
> Url: https://github.com/boostorg/container/issues/300#issuecomment-3261781040  

Fixed with pull #314, many thanks to @nigels-com.
